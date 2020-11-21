# Migration from Elasticsearch 6.8.2 to ES 7.x

---
⚠️ IMPORTANT NOTE

- This migration process is intended for single node of Elasticsearch database
- The current version of this document is provided for testing purpose **ONLY!**  
- This guide has been written and tested to migrate data from ES 6.8.2 to ES 7.8.1, and Cortex 3.0.1 to Cortex 3.1.0 **only!**
- This guide starts with Elasticsearch version 6.8.2  up and running, indexes and data. To test this guide, we recommend using a backup of you production server. (see Backup and Restore page for more information)
- This guide is illustrated with Cortex index. The process is identical for Cortex, you just have to adjust index names.
---

## Prerequisite

The software `jq` is required to manipulate JSON and create new indexes. More information at [https://stedolan.github.io/jq/](). 

## Identify if your index should be reindexed

You can easily identify if indexes should be reindexed or not. On the index named `cortex_4` run the following command: 

```
curl -s http://127.0.0.1:9200/cortex_4?human | jq '.cortex_4.settings.index.version.created'
```

if the output is similar to `"5xxxxxx"`  then reindexing is required, you should follow this guide. 

If it is   `"6xxxxxx"` then the index can be read by Elasticsearch 7.8.x. Upgrade Elasticsearch, and Cortex 3.1.0.

## Migration guide

### Current status

Current context is: 
- Elasticsearch 6.8.2
- Cortex 3.0.1

All up and running. 

Start by identifying indices on you Elasticsearch instance.

```
curl  http://localhost:9200/_cat/indices\?v
```

The output should look like this: 

```
health status index           uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   cortex_4    Y5rDTO23RBC_n6pjFP0-Qw   5   0       8531            8       13mb           13mb 
```


The index name is `cortex_4`. Record this somewhere.

### Stop services

Before starting updating the database, lets stop applications:

```
sudo service cortex stop 
```

### Create a new index


The First operation lies in creating a new index named `new_cortex_4` with settings from current index `cortex_4` (ensure to keep index version, needed for future upgrade).

```bash
curl -XPUT 'http://localhost:9200/new_cortex_4' \
  -H 'Content-Type: application/json' \
  -d "$(curl http://localhost:9200/cortex_4 |\
   jq '.cortex_4 |
   del(.settings.index.provided_name,
    .settings.index.creation_date,
    .settings.index.uuid,
    .settings.index.version,
    .settings.index.mapping.single_type,
    .mappings.doc._all)'
    )"
```


Check the new index is well created: 

```
curl -XGET http://localhost:9200/_cat/indices\?v
```

The output should look like this: 

```
health status index           uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   new_cortex_4    wRX6rhzXTuW_F2wLNxqVyg   5   0          0            0      1.1kb          1.1kb
green  open   cortex_4        Y5rDTO23RBC_n6pjFP0-Qw   5   0       8531            8       13mb           13mb
```

### Proceed to Reindex 

Next operation lies in running the reindex command in the newly created index:

```bash
curl -XPOST -H 'Content-Type: application/json' http://localhost:9200/_reindex -d '{
  "conflicts": "proceed",
  "source": {
    "index": "cortex_4"
  },
  "dest": {
    "index": "new_cortex_4"
  }
}'
```

After a moment, you should get a similar output:  

```json
{
    "took": 5119,
    "timed_out": false,
    "total": 5889,
    "updated": 0,
    "created": 5889,
    "deleted": 0,
    "batches": 6,
    "version_conflicts": 0,
    "noops": 0,
    "retries": {
        "bulk": 0,
        "search": 0
    },
    "throttled_millis": 0,
    "requests_per_second": -1.0,
    "throttled_until_millis": 0,
    "failures": []
}
```

### Ensure new index has been created

Run the following command, and ensure the new index is like the current one (size can vary):

```
curl -XGET http://localhost:9200/_cat/indices\?v
```

The output should look like this: 

```
health status index           uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   new_cortex_4    wRX6rhzXTuW_F2wLNxqVyg   5   0       8531            0     12.6mb         12.6mb
green  open   cortex_4        Y5rDTO23RBC_n6pjFP0-Qw   5   0       8531            8       13mb           13mb
```

### Delete old indices

This is the thrilling part. 
Now the new index `new_cortex_4` is created and similar to `cortex_4`,  older indexes **should be completely deleted** from the database. To delete index named `cortex_4`, run the following command:  

```bash
curl -XDELETE http://localhost:9200/cortex_4
```

Run the same command for older indexes if exist (cortex_3, cortex_2....). Elasticsearch 7.x cannot run with index created with Elasticsearch 5.x.

### Create an alias 

Before stopping Elasticsearch service, let’s create an alias to keep index names in the future.  

```bash
curl -XPOST -H 'Content-Type: application/json'  'http://localhost:9200/_aliases' -d '{
    "actions": [
        {
            "add": {
                "index": "new_cortex_4",
                "alias": "cortex_4"
            }
        }
    ]
}'
```


Doing so will allow Cortex 3.1.0  to find the index without updating the configuration file. 

Check the alias has been well created by running the following command

```bash
curl -XGET http://localhost:9200/_alias?pretty
```

The output should look like:

```json
{
  "new_cortex_4" : {
    "aliases" : {
      "cortex_4" : { }
    }
  }
}
```


## Stop Elasticsearch version 6.8.2

```bash
sudo service elasticsearch stop 
```


## Update Elasticsearch 

Update the configuration of Elastisearch. Configuration file should look like this:

```
[..]
http.host: 127.0.0.1
discovery.type: single-node
cluster.name: hive
script.allowed_types: inline
thread_pool.search.queue_size: 100000
thread_pool.write.queue_size: 10000    
```

Now, upgrade Elasticsearch to version 7.x following the documentation for your Operating System, and ensure the service start successfully.

## Install or update to Cortex 3.1.0

### DEB package

If using Debian based Linux operating system, configure it to follow our beta repository:

```bash
curl https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY | sudo apt-key add -
echo 'deb https://deb.thehive-project.org release main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-get update
```
Then install it by running:

```bash
sudo apt install cortex
```

or

```bash
sudo apt install cortex=3.1.0-1
```

### RPM

Setup your system to connect the RPM repository. Create and edit the file  `/etc/yum.repos.d/thehive-project.repo` :

```
[thehive-project]
enabled=1
priority=1
name=TheHive-Project RPM repository
baseurl=http://rpm.thehive-project.org/release/noarch
gpgcheck=1
```

Then install it by running:

```bash
sudo yum install cortex
```

or 

```bash
sudo yum install cortex-3.1.0-1
```

### Install binaries

```bash
cd /opt
wget https://download.thehive-project.org/cortex-3.1.0-1.zip
unzip cortex-3.1.0-1.zip
ln -s cortex-3.1.0-1 cortex
```

### Docker images

Docker images are also provided on Dockerhub. 

```bash
docker pull thehiveproject/cortex:3.1.0-1
```


⚠️  Starting from this version, docker image doesn't contain analyzers anymore. _Analyzers__/__Responders_ and Cortex have different life-cycles, their update including their dependencies should not be correlated to Cortex update. 

It is recommended to use docker version of analyzers : this can be done by binding docker service docket inside cortex container (run with `-v /var/run/docker.sock:/var/run/docker.sock`).


### Update Database

Connect to TheHive (and Cortex), the maintenance page should ask to update. 

![](/images/thehive-first-access_screenshot.png)

Once updated, ensure a new index named `cortex_5` has been created.


```bash
curl -XGET http://localhost:9200/_cat/indices\?v
```

The output should look like this: 

```
health status index           uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   new_cortex_4 GV-3Y8QjTjWw0F-p2sjW6Q   5   0      30977            0       26mb           26mb
yellow open   cortex_5     Nz0vCKqhRK2xkx1t_WF-0g   5   1      30977            0     26.1mb         26.1mb
```


# Cortex Installation Using Docker

This guide assume that you will use [Docker](https://www.docker.com/).

## How to Use the Docker Image

Cortex 2 requires ElasticSearch to run. You can use docker-compose to start them together in docker or install and configure ElasticSearch manually.

### Use Docker-compose

Docker-compose can start multiple dockers and link them together. It can be installed using the
[documentation](https://docs.docker.com/compose/install/).

The following [docker-compose.yml](https://raw.githubusercontent.com/TheHive-Project/Cortex/master/docker/cortex/docker-compose.yml)
file starts ElasticSearch and Cortex:
```
version: "2"
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.6.0
    environment:
      - http.host=0.0.0.0
      - transport.host=0.0.0.0
      - xpack.security.enabled=false
      - cluster.name=hive
      - script.inline=true
      - thread_pool.index.queue_size=100000
      - thread_pool.search.queue_size=100000
      - thread_pool.bulk.queue_size=100000
  cortex:
    image: certbdf/cortex:latest
    depends_on:
      - elasticsearch
    ports:
      - "0.0.0.0:9001:9001"
```
Put this file in an empty folder and run `docker-compose up`. Cortex is exposed on 9001/tcp port. These ports can be changed by modifying the `docker-compose` file.

You can also use TheHive [docker-compose](https://raw.githubusercontent.com/TheHive-Project/TheHive/master/docker/thehive/docker-compose.yml) file which contains TheHive, Cortex and ElasticSearch, more info [here](https://github.com/TheHive-Project/TheHiveDocs/blob/master/installation/docker-guide.md)

You can specify a custom `application.conf` file by adding the lines, in `cortex` section:
```
volumes:
    - /path/to/application.conf:/etc/cortex/application.conf
```

You should define where the data (i.e. the ElasticSearch database) will be stored in your server by adding the lines, in `elasticsearch` section:
```
volumes:
    - /path/to/data:/usr/share/elasticsearch/data
```

### Manual Installation of ElasticSearch

ElasticSearch can be installed on the same server as TheHive or on a different one. You can then configure TheHive according to the
[documentation](../admin/configuration.md) and run TheHive docker as follow:
```
docker run --volume /path/to/thehive/application.conf:/etc/thehive/application.conf certbdf/thehive:latest --no-config
```

You can add the `--publish` docker option to expose TheHive HTTP service.

## Customize the Docker Image

By default, Cortex docker image adds minimal configuration:
 - choose a random secret (play.http.secret.key)
 - search ElasticSearch instance (host named `elasticsearch`) and add it to configuration

This behavior can be disabled by adding `--no-config` to the docker command line:
`docker run certbdf/cortex:latest --no-config` or by adding the line `command: --no-config` in `cortex` section of
docker-compose file.

The image accepts more options:
 - --no-config             : do not try to configure TheHive (add secret and elasticsearch)
 - --no-config-secret      : do not add random secret to configuration
 - --no-config-es          : do not add elasticsearch hosts to configuration
 - --es-hosts <esconfig>   : use this string to configure elasticsearch hosts (format: ["host1:9300","host2:9300"])
 - --es-hostname <host>    : resolve this hostname to find elasticseach instances
 - --secret <secret>       : secret to secure sessions


Please remember that you must install and configure ElasticSearch.

## Analyzers

Analyzers are embedded in the docker image in `/opt/Cortex-Analyzers/analyzers`. If you want to update them, you should
install them outside docker and overwrite the existing ones by adding the parameter:
```
--volume /path/to/analyzers:/opt/Cortex-Analyzers/analyzers:ro certbdf/cortex:latest  
```

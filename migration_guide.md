# Migration Guide

## From 3.0.x to 3.1.0

Taking into account the EoL of version 6.x. of Elasticsearch, Cortex 3.1.0 is the first version to support Elasticsearch 7.x. This version introduce breaking changes.  This time,  we had no choice, we were not able to make TheHive support smoothly the ES upgrade. 

Cortex 3.1.0 supports Elasticsearch 7.x **ONLY**.

This first steps before starting the upgrade process are: 

- Identify the version of Elasticsearch which created your index
- Stop Cortex service
- Stop Elasticsearch service

### How to identify the version of Elasticsearch which created your database index ?

---

The software `jq` is required to manipulate JSON and create new indexes. More information at [https://stedolan.github.io/jq/](). 

---

Run the following command : 

```bash
curl -s http://127.0.0.1:9200/cortex_4?human | jq '.cortex_4.settings.index.version.created'
```

- if the output is similar to `"5xxxxxx"`  then your database index has been created with Elasticsearch 5.x  reindexing is required, you should follow [a dedicated process to upgrade](../admin/upgrade_to_thehive_3_5_and_es_7_x.md). 
- If it is   `"6xxxxxx"` then your database has been created with Elasticsearch 6.

### Your database was created with Elasticsearch 5.x or earlier

This is where things might be complicated. This upgrade progress  requires handling the database index by updating parameters, and reindex before updating Elasticsearch, and updating TheHive.

Read carefully [the dedicated documentation](../admin/upgrade_to_cortex_3_1_and_es7_x). It should help you run this specific actions on your Elasticsearch database, and also install or update application whether you are using DEB, RPM or binary packages, and even docker images.

### Your database was created with Elasticsearch 6.x

If you started using TheHive with Elasticsearch 6.x, then you just need to update the configuration of Elasticsearch to reflect this one: 

```
[..]
http.host: 127.0.0.1
discovery.type: single-node
cluster.name: hive
script.allowed_types: inline
thread_pool.search.queue_size: 100000
thread_pool.write.queue_size: 10000
```

Following parameters are **not accepted anymore** by Elasticsearch 7: 

- `thread_pool.index.queue_size`
- `thread_pool.bulk.queue_size` 

With TheHive service stopped, ensure the new version of Elasticsearch starts.

If everything is ok, then Cortex 3.1.0 can be installed. To run this operation successfully, you need to **update your repository configuration**  if you are using DEB and RPM packages, or specify the right version to install if using docker. Read carefully the [installation guide](../installation/install-guide.md). 



## From 2.x to 3.x

### Elasticsearch Changes
Starting from version 3.0.0-RC3, Cortex supports Elasticsearch 6 and will continue to work with Elasticsearch 5.x.

Cortex 3.0.0-RC3 and later versions communicate with Elasticsearch using its HTTP service (9200/tcp by default) instead of its legacy binary protocol (9300/tcp by default). If you have a firewall between Cortex and Elasticsearch, you probably need to update its rules to change to the new port number.

The configuration file (`application.conf`) needs some modifications to reflect the protocol change:

- The setting `search.host` is replaced by `search.uri`
- The general format of the URI is: `http(s)://host:port,host:port(/prefix)?querystring`. Multiple `host:port` combinations can be specified, separated by commas. Options can be specified using a standard URI query string syntax, eg. `cluster.name=hive`.
- The `search.cluster`setting is no longer used.
- Authentication can be configured with the `search.user` and `search.password` settings.

When SSL/TLS is enabled, you can set a truststore and a keystore. The truststore contains the certificate authorities used to validate remote certificates. The keystore contains the certificate and the private key used to connect to the Elasticsearch cluster. The configuration is:
```hocon
search {
  keyStore {
    path: "/path/to/keystore/file"
    type: "JKS" # or PKCS12
    password: "secret.password.of.keystore"
  }
  trustStore {
    path: "/path/to/truststore/file"
    type: "JKS"
    password: "secret.password.of.truststore"
  }
}
```

The Elasticsearch client also accepts the following settings:
 - `circularRedirectsAllowed` (`true`/`false`)
 - `connectionRequestTimeout` (number of seconds)
 - `connectTimeout`
 - `contentCompressionEnabled.foreach(requestConfigBuilder.setContentCompressionEnabled)`
 - `search.cookieSpec` (??)
 - `expectContinueEnabled` (`true`/`false`)
 - `maxRedirects` (number)
 - `proxy` -- not yet supported
 - `proxyPreferredAuthSchemes` -- not yet supported
 - `redirectsEnabled` (`true`/`false`)
 - `relativeRedirectsAllowed` (`true`/`false`)
 - `socketTimeout` (number of seconds)
 - `targetPreferredAuthSchemes` (??)

The configuration items `keepalive`, `pageSize`, `nbshards` and `nbreplicas` are still valid.

### New Requirements
Cortex 3 requires at least Cortexutils 2.0 because communication between Cortex and the analyzers/responders has changed [\#176](https://github.com/TheHive-Project/Cortex/issues/176). Cortexutils 2.x is compatible with previous versions of Cortex.

### Docker vs. Process
Analyzers can now be started in a Docker container. This permits a better isolation between analyzers and simplify the installation of their respective requirements and their update.

By default, Cortex try to run analyzers using docker, thus the [docker service](https://docs.docker.com/install/) must be installed and the user under which Cortex is running must be able to use it. Normally, adding that user to the `docker` user group should be sufficient. Then Cortex will accept dockerised analyzers.

### Analyzer and Responder Catalog

The settings `analyzer.path` and `responder.path` are deprecated but will continue to work. They have been replaced by `analyzer.urls` and `responder.urls`. These settings can contain:
   - the paths/directories where workers (analyzers and  responders) are installed;
   - the path or URL (*http(s)*) of a JSON file containing all worker definitions (merge of all JSONs in one array).

We provide a file, hosted on GitHub, containing all the analyzers of the [Cortex-Analyzers](https://github.com/TheHive-Project/Cortex-Analyzers)repository:
 - [analyzers-devel.json](https://download.thehive-project.org/analyzers-devel.json): contains the latest versions of the analyzers, **before their release**. Each commit in the develop branch update the docker images used by this catalog.
 - [analyzers-stable.json](https://download.thehive-project.org/analyzers-stable.json): contains the stable versions of the analyzers. They are never been updated. The Analyzer ID points to an exact version of the analyzer. If you want to use a new version of an analyzer, you can but it is not done automatically. You need to enable it in the organisation administration page of the Cortex Web UI.
 - [analyzers.json](https://download.thehive-project.org/analyzers.json): Docker images in this catalog contains minor version updates (without breaking changes).

Similar catalogs exist for responders:

-  [responders-devel.json](https://download.thehive-project.org/responders-devel.json)
- [responders-stable.json](https://download.thehive-project.org/responders-stable.json)
- [responders.json](https://download.thehive-project.org/responders.json).

By default, before start a job, Cortex checks if there is a new version of the Docker image available on the repository. If so, it will download and use it. This behavior can be disabled by setting `docker.autoUpdate` to false.

The JSON files can have a  `command` (can be run using the old-fashioned process runner) and a `dockerImage` attribute. The latter indicates the Docker image to use when running the related analyzer/responder. If it contains both, the runner is chosen according to the `job.runners` setting (`[docker, process]` by default). This setting defines how a job will be run. **The order is important**.

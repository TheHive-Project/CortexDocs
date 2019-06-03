# Migration Guide

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

By default, Cortex continues to run analyzers using processes, like before. To run the dockerised version of analyzers and responders, the [docker service](https://docs.docker.com/install/) must be installed and the user under which Cortex is running must be able to use it. Normally, adding that user to the `docker` user group should be sufficient. Then Cortex will accept dockerised analyzers.

### Analyzer and Responder Catalog

The settings `analyzer.path` and `responder.path` are deprecated but will continue to work. They have been replaced by `analyzer.urls` and `responder.urls`. These settings can contain:
   - the paths/directories where workers (analyzers and  responders) are installed;
   - the path or URL (*http(s)*) of a JSON file containing all worker definitions (merge of all JSONs in one array).

We provide a file, hosted on GitHub, containing all the analyzers of the [Cortex-Analyzers](https://github.com/TheHive-Project/Cortex-Analyzers)repository:
 - [analyzers/catalog-devel.json](https://raw.githubusercontent.com/TheHive-Project/Cortex-Analyzers/develop/analyzers/catalog-devel.json): contains the latest versions of the analyzers, **before their release**. Each commit in the develop branch update the docker images used by this catalog. 
 - [analyzers/catalog-stable.json](https://raw.githubusercontent.com/TheHive-Project/Cortex-Analyzers/master/analyzers/catalog-stable.json): contains the stable versions of the analyzers. They are never been updated. The Analyzer ID points to an exact version of the analyzer. If you want to use a new version of an analyzer, you can but it is not done automatically. You need to enable it in the organisation administration page of the Cortex Web UI.
 - [analyzers/catalog.json](https://raw.githubusercontent.com/TheHive-Project/Cortex-Analyzers/master/analyzers/catalog.json): Docker images in this catalog contains minor version updates (without breaking changes).

Similar catalogs exist for responders:

-  [responders/catalog-devel.json](https://raw.githubusercontent.com/TheHive-Project/Cortex-Analyzers/develop/responders/catalog-devel.json)
- [responders/catalog-stable.json](https://raw.githubusercontent.com/TheHive-Project/Cortex-Analyzers/master/responders/catalog-stable.json)
- [responders/catalog.json](https://raw.githubusercontent.com/TheHive-Project/Cortex-Analyzers/master/responders/catalog.json).

The JSON files can have a  `command` (can be run using the old-fashioned process runner) and a `dockerImage` attribute. The latter indicates the Docker image to use when running the related analyzer/responder. If it contains both, the runner is chosen according to the `job.runners` setting (`[docker, process]` by default). This setting defines how a job will be run. **The order is important**.

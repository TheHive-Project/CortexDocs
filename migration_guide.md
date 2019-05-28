# Migration guide

## From 2.x to 3.x

### ElasticSearch changes
From version 3.0.0-RC3, Cortex supports ElasticSearch 6 (and continues to work with ElasticSearch 5.x). Cortex now uses HTTP communication (9200/tcp by default) instead of binary protocol (9300/tcp by default). If you have a firewall between Cortex and ElasticSearch, you probably need to update its rules to allow 9200/tcp.

The configuration file (application.conf) need some modification to reflect the protocol change. The setting `search.host` is replaced by `search.uri`. The general format of the URI is: `http(s)://host:port,host:port(/prefix)?querystring`. Multiple host:port combinations can be specified, separated by commas. Options can be specified using standard uri query string syntax, eg cluster.name=hive. The `search.cluster` setting  is not used any more.

Authentication can be configured with the setting `search.user` and `search.password`.

When SSL is enable, you can set a truststore and a keystore. The truststore contains the certificate authorities used to validate remote certificate. The keystore contain the certificate and the private key used to connect to ElasticSearch cluster. The configuration is:
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

The ElasticSearch client also accepts the following settings:
 - circularRedirectsAllowed (true/false)
 - connectionRequestTimeout (number of seconds)
 - connectTimeout
 - contentCompressionEnabled").foreach(requestConfigBuilder.setContentCompressionEnabled)
 - search.cookieSpec (??)
 - expectContinueEnabled (true/false)
 - maxRedirects (number)
 - proxy -- not yet supported
 - proxyPreferredAuthSchemes -- not yet supported
 - redirectsEnabled (true/false)
 - relativeRedirectsAllowed (true/false)
 - socketTimeout (number of seconds)
 - targetPreferredAuthSchemes (??) 

The configuration items `keepalive`, `pageSize`, `nbshards` and `nbreplicas` are still valid.

### New requirements
Cortex 3 requires new version of CortexUtils (at least version 2.0) because communication between Cortex and the workers has changed [\#176](https://github.com/TheHive-Project/Cortex/issues/176). Cortexutils 2.x is compatible with previous version of Cortex

### Docker Vs Process
Analyzers can now be started in a Docker container. This permits a better isolation between analyzers and simplify the installation of requirements.

By default, Cortex continues to run analyzers using processes, like before. To run docker version of analyzers, the docker service must be installed (https://docs.docker.com/install/) and Cortex user must be able to use it (add cortex user in docker group should be enough). Then Cortex will accept dockerized analyzers.

### Catalog of analyzers and responders

The settings `analyzer.path` and `responder.path` are deprecated (but continue to work) and are replaced by `analyzer.urls` and `responder.urls`. These settings can contain:
   - path of directory where workers (analyzers/responders) are installed (like previous version of Cortex)
   - path or url (http(s)) of JSON file containing all worker definitions (merge of all JSON in one array)

We provide a file (hosted in github) containing all analyzers from Cortex-Analyzer repository:
 - [analyzers/catalog-devel.json](https://github.com/TheHive-Project/Cortex-Analyzers/tree/develop/analyzers/catalog-devel.json): contains latest versions of analyzers, before their release. Each commit in develop branch update the docker image used by this catalog. 
 - [analyzers/catalog-stable.json](https://github.com/TheHive-Project/Cortex-Analyzers/tree/develop/analyzers/catalog-stable.json):
 contains stable versions of analyzers. They are never been updated. Analyzer ID points on a exact version of the analyzer. If you want to use a new version of analyzer, you can but it is not done automatically. You need to enable it in the organisation administration page.
 - [analyzers/catalog.json](https://github.com/TheHive-Project/Cortex-Analyzers/tree/develop/analyzers/catalog.json): Docker image in this catalog contains minor version update (without breaking changes).

The similar catalogs exist for responders: [responders/catalog-devel.json](https://github.com/TheHive-Project/Cortex-Analyzers/tree/develop/responders/catalog-devel.json), [responders/catalog-stable.json](https://github.com/TheHive-Project/Cortex-Analyzers/tree/develop/responders/catalog-stable.json), [responders/catalog.json](https://github.com/TheHive-Project/Cortex-Analyzers/tree/develop/responders/catalog.json).

The definitions (JSON file) can have `command` attribute (can be run using process runner) and `dockerImage` attribute. The latter indicates the Docker image to use when running the related analyzer/responder. If it contains both, the runner is choosen according to `job.runners` settings (`[docker, process]` by default). This setting defines how job will be run. The order is important.

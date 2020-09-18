# Administration Guide
This administration guide applies to Cortex 2 only.

**Note**: you will need TheHive 3.0.7 or later to call Cortex 2 from TheHive. Earlier versions support only Cortex 1.

## Table of Contents
  * [User Roles](#user-roles)
    * [On First Access](#on-first-access)
  * [Organizations, Users and Analyzers](#organizations-users-and-analyzers)
    * [Organizations](#organizations)
      * [Can Organizations be Deleted?](#can-organizations-be-deleted)
    * [Users](#users)
      * [Can Users be Deleted?](#can-users-be-deleted)
    * [Analyzers](#analyzers)
  * [Application Configuration](#application-configuration)
    * [Database](#database)
    * [Analyzers](#analyzers-1)
    * [Authentication](#authentication)
    * [Cache](#cache)
       * [Performance](#performance)
       * [Analyzer Results](#analyzer-results)
    * [Streaming (a\.k\.a The Flow)](#streaming-aka-the-flow)
      * [Entity Size Limit](#entity-size-limit)
    * [HTTPS](#https)

## User Roles
Cortex 2 introduces a number of significant changes over Cortex 1. One of them is role-based access control. Cortex 2 defines four roles:
 - `read`: the user can access all the jobs that have been performed by the Cortex 2 instance, including their results. However, this role **cannot** submit jobs. Moreover, this role **cannot** be used in the default `cortex` organization. This organization can only contain super administrators.
 - `analyze`: the `analyze` role implies the `read` role, described above. A user who has a `analyze` role can submit a new job using one of the
configured analyzers for their organization. This role **cannot** be used in the default `cortex` organization. This organization can only contain super administrators.
 - `orgAdmin`: the `orgAdmin` role implies the `analyze` role. A user who has an `analyze` role can manage users
within their organization. They can add users and give them `read`, `analyze` and/or `orgAdmin` roles.
This role also permits to configure analyzers for the organization. This role **cannot** be used in the default  `cortex` organization. This organization can only contain super administrators.
 - `superAdmin`: this role is incompatible with all the other roles listed above (see chart below for examples). It can be used solely for managing organizations and their associated users. When you install Cortex, the first user that is created will have this role. Several users can have it as well but only in the default `cortex` organization, which is automatically created during installation.

The chart below lists the roles and what they can and cannot do:

| Actions                  | read | analyze | orgAdmin | superAdmin |
| ------------------------ | ---- | ------- | -------- | ---------- |
| Read reports             |  X   |    X    |    X     |            |
| Run jobs                 |      |    X    |    X     |            |
| Enable/Disable analyzer  |      |         |    X     |            |
| Configure analyzer       |      |         |    X     |            |
| Create org analyst       |      |         |    X     |     X      |
| Delete org analyst       |      |         |    X     |     X      |
| Create org admin         |      |         |    X     |     X      |
| Delete org admin         |      |         |    X     |     X      |
| Create Org               |      |         |          |     X      |
| Delete Org               |      |         |          |     X      |
| Create Cortex admin user |      |         |          |     X      |

### On First Access
If the database is not yet initialized (or initialized with obsolete data), Cortex will ask you to migrate the database. This operation ensures that its schema is up-to-date, then retrieves previous data (if any) and update
its format for the new schema.

![update](../images/update.png)

At the end of this process, Cortex asks for the first user creation. Please remember that this user will possess `superAdmin` powers.

![first user creation](../images/first_user_creation.png)

## Organizations, Users and Analyzers
Upon installation, Cortex 2 creates a default organization called `cortex`. The `cortex` organization cannot be used for any other purpose than managing organizations and their users. It contains the user that is created on first access and any other user that is created with a `superAdmin` role. All other users (`read`, `analyze` and `orgAdmins` must belong to organizations other than `cortex`). Those users can only see items within their own organization.

Analyzers are enabled then configured using the Web user interface for each organization. That way, an analyzer can
be configured using different API keys for each organization. Analyzer rate limiting, when applicable, can also be configured per organization.

### Organizations
Users with the `superAdmin` role can create organizations. Organization management is performed through the *Organizations* menu.

As stated earlier, the `cortex` organization has a special meaning. It is automatically created when Cortex is initialized and can only contain `superAdmin` users. Normal organizations hold users and analyzer configuration.

#### Can Organizations be Deleted?
Administrators, beware! An organization cannot be deleted once created but it can be disabled by a `superAdmin`. In that case, all operations users in that organization would try to perform will be rejected.

If needed, a `superAdmin` can re-enable a disabled organization.

### Users
User accounts can be managed by a `superAdmin` in any organization that exists in the Cortex instance. Users can also be managed for a specific organization by those who possess the `orgAdmin` role in **that** organization.

User management is done in the *Organizations* > *Users* tab.

![users](../images/users.png)

#### Can Users be Deleted?
User accounts cannot be deleted once created but they can be locked by an `orgAdmin` or a `superAdmin`. Once locked, they cannot be used.

If needed, an `orgAdmin` or a `superAdmin` can unlock a locked user account.

### Analyzers and Responders
Analyzers and responders can be enabled, disabled and configured only by `orgAdmin` users. `superAdmins` roles cannot do that.

Analyzer management is done in two locations:

- Under the *Organization* > *Analyzers Config* tab, `orgAdmin` users can define the configuration for all the available analyzers, including settings which are common to all the flavors of a given analyzer.
- Under the *Organization* > *Analyzers* tab, `orgAdmin` users can disable, enable and configure specific analyzer flavors. They can override the global configuration inherited from the *Organization* > *Analyzers Config* tab and add additional, non-global configuration that some analyzer flavors might need to work correctly.
- Under the *Organization* > *Responders Config* tab, `orgAdmin` users can define the configuration for all the available responders, including settings which are common to all the flavors of a given responders.
- Under the *Organization* > *Responders* tab, `orgAdmin` users can disable, enable and configure specific responder flavors. They can override the global configuration inherited from the *Organization* > *Responders Config* tab and add additional, non-global configuration that some responder flavors might need to work correctly.

**Important Note**:

![analyzer configuration](../images/analyzer_config.png)

The configuration can only be seen by `orgAdmin` users of a given
organization. `superAdmin` users cannot view analyzer configuration.

![analyzers](../images/analyzers.png)

Under the *Organization* > *Analyzers* tab, analyzers and their flavors can be enabled, disabled and configured for the current organization. For each one of them, you can define a rate limit, i.e. the maximum number of analysis jobs that can be executed for that specific analyzer/flavor in the specified period of time, and the maximum acceptable observable TLP the analyzer can process.

**Important Note**:
Please note that, by default, no analyzer is enabled nor configured, even the free ones or those that do not need any configuration. It is up to each `orgAdmin` to enable the analyzers for their organization, configure them when applicable and apply rate limits if they want to do so.

## Application Configuration
As described in the section above, Analyzers can only be configured using the Web interface and their associated configuration is stored in the underlying Elasticsearch database. However, the Cortex appplication configuration is stored in the `/etc/cortex/application.conf` file.

### Database

Cortex relies on the Elasticsearch 5.x (Cortex 3 also supports Elasticsearch 6.x) search engine to store all persistent data.
Elasticsearch is not part of the Cortex package. It must be installed and configured
as a standalone instance which can be located on the same machine. For more
information on how to set up Elasticsearch, please refer to [Elasticsearch
installation guide](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/_installation.html).

Three settings are required to connect to Elasticsearch:
 * the base name of the index
 * the name of the cluster
 * the address(es) and port(s) of the Elasticsearch instance

The default settings are:
```
# Elasticsearch
search {
  # Name of the index
  index = cortex
  # Name of the Elasticsearch cluster
  cluster = hive
  # Address of the Elasticsearch instance
  host = ["127.0.0.1:9300"]
  # Scroll keepalive
  keepalive = 1m
  # Size of the page for scroll
  pagesize = 50
  # Number of shards
  nbshards = 5
  # Number of replicas
  nbreplicas = 1
  # Arbitrary settings
  settings {
    # Maximum number of nested fields
    mapping.nested_fields.limit = 100
  }

  ### XPack SSL configuration
  # Username for XPack authentication
  #search.username
  # Password for XPack authentication
  #search.password
  # Enable SSL to connect to ElasticSearch
  search.ssl.enabled = false
  # Path to certificate authority file
  #search.ssl.ca
  # Path to certificate file
  #search.ssl.certificate
  # Path to key file
  #search.ssl.key

  ### SearchGuard configuration
  # Path to JKS file containing client certificate
  #search.guard.keyStore.path
  # Password of the keystore
  #search.guard.keyStore.password
  # Path to JKS file containing certificate authorities
  #search.guard.trustStore.path
  ## Password of the truststore
  #search.guard.trustStore.password
  # Enforce hostname verification
  #search.guard.hostVerification
  # If hostname verification is enabled specify if hostname should be resolved
  #search.guard.hostVerificationResolveHostname
}
```

If you use a different configuration, please make sure to modify the parameters accordingly in the
`application.conf` file.

If multiple Elasticsearch nodes are used as a cluster, addresses of the master
nodes must be used for the `search.host` setting. All cluster nodes must use the
same cluster name:

```
search {
    host = ["node1:9300", "node2:9300"]
   ...
```

Cortex uses the [TCP transport](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/modules-network.html#_transport_and_http_protocols)
port (9300/tcp by default). Cortex cannot use the HTTP transport as of this writing (9200/tcp).

Cortex creates specific index schema (mapping) versions in Elasticsearch. Version numbers are
appended to the index base name (the 8th version of the schema uses the index
`cortex_8` if `search.index = cortex`). When too many documents are requested, it uses the
[scroll](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-request-scroll.html)
feature: the results are retrieved through pagination. You can specify the size
of the page (`search.pagesize`) and how long pages are kept in Elasticsearch
(`search.keepalive`) before purging.

XPack and SearchGuard are optional and exclusive. If Cortex finds a valid configuration for XPack, SearchGuard configuration is ignored.

### Analyzers and Responders
Cortex is able to run workers (analyzers and responders) installed locally or available as Docker image. Settings `analyzer.urls` and in `responder.urls` list paths or urls where Cortex looks for analyzers and responders. Theses settings accept:
1. a path to a directory that Cortex scans to locate workers
1. a path or an URL to a JSON file containing a JSON array of worker definitions

Worker definition is a JSON object that describe the worker, how to configure it and how to run it. If it contains a field "command", worker can be run using process runner (i.e. the command is executed). If it contains a field "dockerImage", worker can be run using docker runner (i.e. a container based on this image is started). If it contains both, the runner is chosen according to `job.runners` settings (`[docker, process]` by default).

For security reason, if worker definitions fetched from remote url (http/https) contain command, they are ignored.

You can control the number of simultaneous jobs that Cortex executes in parallel using the
`analyzer.fork-join-executor` configuration item. The value depends on the
number of CPU cores (`parallelism-factor` * nbCores), with a minimum
(`parallelism-min`) and a maximum (`parallelism-max`).

Similar settings can also be applied to responders.

```
analyzer {
  # Directory that holds analyzers
  urls = [
    "/path/to/default/analyzers",
    "/path/to/my/own/analyzers"
  ]

  fork-join-executor {
    # Min number of threads available for analyze
    parallelism-min = 2
    # Parallelism (threads) ... ceil(available processors * factor)
    parallelism-factor = 2.0
    # Max number of threads available for analyze
    parallelism-max = 4
  }
}

responder {
  # Directory that holds responders
  urls = [
    "/path/to/default/responders",
    "/path/to/my/own/responders"
  ]

  fork-join-executor {
    # Min number of threads available for analyze
    parallelism-min = 2
    # Parallelism (threads) ... ceil(available processors * factor)
    parallelism-factor = 2.0
    # Max number of threads available for analyze
    parallelism-max = 4
  }
}
```

### Authentication
Like TheHive, Cortex supports local, LDAP, Active Directory (AD), X.509 SSO and/or API keys for authentication and OAuth2.

Please note that API keys can only be used to interact with the Cortex API (for example when TheHive is interfaced with a Cortex instance, it must use an API key to authenticate to it). API keys cannot be used to authenticate to the Web UI. By default, Cortex relies on local credentials stored in Elasticsearch.

Authentication methods are stored in the `auth.provider` parameter, which is
multi-valued. When a user logs in, each authentication method is tried in order
until one succeeds. If no authentication method works, an error is returned and
the user cannot log in.

The default values within the configuration file are:
```
auth {
	# "provider" parameter contains authentication provider. It can be multi-valued (useful for migration)
	# available auth types are:
	# services.LocalAuthSrv : passwords are stored in user entity (in Elasticsearch). No configuration is required.
	# ad : use ActiveDirectory to authenticate users. Configuration is under "auth.ad" key
	# ldap : use LDAP to authenticate users. Configuration is under "auth.ldap" key
  # oauth2 : use OAuth/OIDC to authenticate users. Configuration is under "auth.oauth2" and "auth.sso" keys
	provider = [local]

  # By default, basic authentication is disabled. You can enable it by setting "method.basic" to true.
  method.basic = false

	ad {
		# The name of the Microsoft Windows domain using the DNS format. This parameter is required.
		#domainFQDN = "mydomain.local"

    # Optionally you can specify the host names of the domain controllers. If not set, Cortex uses "domainFQDN".
    #serverNames = [ad1.mydomain.local, ad2.mydomain.local]

		# The Microsoft Windows domain name using the short format. This parameter is required.
		#domainName = "MYDOMAIN"

		# Use SSL to connect to the domain controller(s).
		#useSSL = true
	}

	ldap {
		# LDAP server name or address. Port can be specified (host:port). This parameter is required.
		#serverName = "ldap.mydomain.local:389"

    # If you have multiple ldap servers, use the multi-valued settings.
    #serverNames = [ldap1.mydomain.local, ldap2.mydomain.local]

		# Use SSL to connect to directory server
		#useSSL = true

		# Account to use to bind on LDAP server. This parameter is required.
		#bindDN = "cn=cortex,ou=services,dc=mydomain,dc=local"

		# Password of the binding account. This parameter is required.
		#bindPW = "***secret*password***"

		# Base DN to search users. This parameter is required.
		#baseDN = "ou=users,dc=mydomain,dc=local"

		# Filter to search user {0} is replaced by user name. This parameter is required.
		#filter = "(cn={0})"
	}

  oauth2 {
    # URL of the authorization server
    #clientId = "client-id"
    #clientSecret = "client-secret"
    #redirectUri = "https://my-cortex-instance.example/api/ssoLogin"
    #responseType = "code"
    #grantType = "authorization_code"

    # URL from where to get the access token
    #authorizationUrl = "https://auth-site.com/OAuth/Authorize"
    #tokenUrl = "https://auth-site.com/OAuth/Token"

    # The endpoint from which to obtain user details using the OAuth token, after successful login
    #userUrl = "https://auth-site.com/api/User"
    #scope = "openid profile"
  }

  # Single-Sign On
  sso {
    # Autocreate user in database?
    #autocreate = false

    # Autoupdate its profile and roles?
    #autoupdate = false

    # Autologin user using SSO?
    #autologin = false

    # Name of mapping class from user resource to backend user ('simple' or 'group')
    #mapper = group
    #attributes {
    #  login = "user"
    #  name = "name"
    #  groups = "groups"
    #  organization = "org"
    #}
    #defaultRoles = ["read"]
    #defaultOrganization = "csirt"
    #groups {
    #  # URL to retreive groups (leave empty if you are using OIDC)
    #  #url = "https://auth-site.com/api/Groups"
    #  # Group mappings, you can have multiple roles for each group: they are merged
    #  mappings {
    #    admin-profile-name = ["admin"]
    #    editor-profile-name = ["write"]
    #    reader-profile-name = ["read"]
    #  }
    #}

    #mapper = simple
    #attributes {
    #  login = "user"
    #  name = "name"
    #  roles = "roles"
    #  organization = "org"
    #}
    #defaultRoles = ["read"]
    #defaultOrganization = "csirt"
  }

}

# Maximum time between two requests without requesting authentication
session {
  warning = 5m
  inactivity = 1h
}
```

### Cache
#### Performance
In order to increase Cortex performance, a cache is configured to prevent
repetitive database solicitation. Cache retention time can be configured for
users and organizations (default is 5 minutes). If a user is updated, the cache is
automatically invalidated.

#### Analyzer Results
Analyzer results (job reports) can also be cached. If an analyzer is executed against the same observable,
the previous report can be returned without re-executing the analyzer. The cache is used only
if the second job occurs within `cache.job` (the default is 10 minutes).
```
cache {
  job = 10 minutes
  user = 5 minutes
  organization = 5 minutes
}
```
**Note**: the global `cache.job` value can be overridden for each analyzer in the analyzer configuration Web dialog.

**Note**: it is possible to bypass the cache altogether (for example to get extra fresh results) through the API as explained in the [API Guide](../api/api-guide.md#run) or by setting the cache to *Custom* in the Cortex UI for each analyzer and specifying `0` as the number of minutes.

### Streaming (a.k.a The Flow)
The user interface is automatically updated when data is changed in the
back-end. To do this, the back-end sends events to all the connected front-ends.
The mechanism used to notify the front-end is called long polling and its
settings are:

 * `refresh` : when there is no notification, close the connection after this
 duration (the default is 1 minute).
 * `cache` : before polling a session must be created, in order to make sure no
 event is lost between two polls. If there is no poll during the cache setting,
 the session is destroyed (the default is 15 minutes).
 * `nextItemMaxWait`, `globalMaxWait` : when an event occurs, it is not
 immediately sent to the front-ends. The back-end waits nextItemMaxWait and up
 to globalMaxWait in case another event can be included in the notification.
 This mechanism saves many HTTP requests.

The default values are:
```
# Streaming
stream.longpolling {
  # Maximum time a stream request waits for new element
  refresh = 1m
  # Lifetime of the stream session without request
  cache = 15m
  nextItemMaxWait = 500ms
  globalMaxWait = 1s
}
```

#### Entity Size Limit
The Play framework used by Cortex sets the HTTP body size limit to 100KB by
default for textual content (json, xml, text, form data) and 10MB for file
uploads. This could be too small in some cases so you may want to change it with
the following settings in the `application.conf` file:

```
# Max textual content length
play.http.parser.maxMemoryBuffer=1M
# Max file size
play.http.parser.maxDiskBuffer=1G
```

**Note**: if you are using a NGINX reverse proxy in front of Cortex, be aware
that it doesn't distinguish between text data and a file upload. So, you should
also set the `client_max_body_size` parameter in your NGINX server configuration
to the highest value among the two: file upload and text size as defined in Cortex
`application.conf` file.

### HTTPS
Enable HTTPS directly on Cortex is not supported anymore. You must install a reverse proxy in front of Cortex.
Below an example of NGINX configuration:
```
	server {
			listen 443 ssl;
			server_name cortex.example.com;

			ssl_certificate			ssl/cortex_cert.pem;
			ssl_certificate_key		ssl/cortex_key.pem;

			proxy_connect_timeout   600;
			proxy_send_timeout      600;
			proxy_read_timeout      600;
			send_timeout            600;
			client_max_body_size    2G;
			proxy_buffering off;
			client_header_buffer_size 8k;

			location / {
					add_header				          Strict-Transport-Security "max-age=31536000; includeSubDomains";
					proxy_pass                  http://127.0.0.1:9001/;
					proxy_http_version          1.1;
					proxy_set_header Connection "";
			}
	}
```

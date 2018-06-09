# FAQ

## Table of Contents
  * [Creation of the First Super Administrator Account](#creation-of-the-first-super-administrator-account)
  * [Analyzers](#analyzers)
    * [What Version of Maxmind Cortex Uses?](#what-version-of-maxmind-cortex-uses)
    * [How often are the MaxMind Databases Refreshed?](#how-often-are-the-maxmind-databases-refreshed)
    * [How Shall I Configure the MaxMind Analyzer?](#how-shall-i-configure-the-maxmind-analyzer)
    * [Can I Use the Commercial Versions of the Databases?](#can-i-use-the-commercial-versions-of-the-databases)
  * [Miscellaneous Questions](#miscellaneous-questions)
    * [Can I Enable HTTPS to Connect to Cortex?](#can-i-enable-https-to-connect-to-cortex)

## Creation of the First Super Administrator Account
After installing Cortex, the first connection to the Web UI triggers a database update. After that operation, you'll be prompted to create a super administrator account (`superAdmin`). If this prompt is not completed, there is no way to set the admin password. Hence it will be impossible to connect to Cortex. To reset the operation, you need to delete the corresponding index from Elasticsearch. First, find out what is the current index of Cortex by running the following command on a host where the Elasticsearch DB used by Cortex is located:

```bash
$ curl http://127.0.0.1:9200/_cat/indices?v
```

The indexes that Cortex uses always start with`cortex_` following by a number. Let's assume that the output of the command is:

```bash
health status index       uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   cortex_1    PC_pLFGBS5G2TNQYr4ajgw   5   1        609            6      2.1mb          2.1mb
yellow open   the_hive_13 ft7GGTfhTr-4lSzZw5r1DQ   5   1     180131            3     51.3mb         51.3mb
```

In this example, the index used by Cortex is `cortex_1`. To delete it, run the following command (**Warning**: this will delete everything):

```bash
$ curl -X DELETE http://127.0.0.1:9200/cortex_1
```

Then reload the page or restart Cortex.

## Analyzers
### What Version of Maxmind Cortex Uses?
The MaxMind analyzer includes the GeoLite2 **free** City and Country databases.

### How often are the MaxMind Databases Refreshed?
Cortex does not refresh those databases. It is up to you to create a cron job to refresh them at the frequency you want. The files to update are:
- `analyzers/MaxMind/GeoLite2-City.mmdb`
- `analyzers/MaxMind/GeoLite2-Country.mmdb`

You can fetch up-to-date versions from <https://dev.maxmind.com/geoip/geoip2/geolite2/>.

### How Shall I Configure the MaxMind Analyzer?
No configuration is required. If it looks like the analyzer is not working, please clear the cache of your browser and retry. If it still doesn't work, please join [TheHive User Discussion Forum](https://groups.google.com/a/thehive-project.org/d/forum/users) or [open an issue on GitHub](https://github.com/TheHive-Project/Cortex-analyzers/issues/new).

### Can I Use the Commercial Versions of the Databases?
The current version of Cortex does not offer that possibility. If you'd like to have it, please [request it](https://github.com/TheHive-Project/Cortex-analyzers/issues/new).

## Miscellaneous Questions
### Can I Enable HTTPS to Connect to Cortex?
Add  the following lines to `/etc/cortex/application.conf`:
```
https.port: 9443
play.server.https.keyStore {
  path: "/path/to/keystore.jks"
  type: "JKS"
  password: "password_of_keystore"
}
```

HTTP can disabled by adding line `http.port=disabled`.

* To import your certificate in the keystore, depending on your situation, you can follow [Digital Ocean's tutorial](https://www.digitalocean.com/community/tutorials/java-keytool-essentials-working-with-java-keystores).
* Make sure the keystore file is owned/can be accessed by the user running Cortex.

**Additional information**:
This is a setting of the Play framework that is documented on its website. Please refer to [https://www.playframework.com/documentation/2.5.x/ConfiguringHttps](https://www.playframework.com/documentation/2.5.x/ConfiguringHttps).
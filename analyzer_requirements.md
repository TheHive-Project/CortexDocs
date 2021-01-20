# Cortex Analyzer & Responder Requirements Guide
Analyzers and Responders are autonomous applications managed by and run through the Cortex core
engine. Analyzers allow analysts and security researchers to analyze
observables and IOCs such as
domain names, IP addresses, hashes, files, URLs at scale. While many
analyzers are free to use, some require special access while others
necessitate a valid service subscription or product license, even though the
analyzers themselves are released under an the [AGPL](https://github.com/TheHive-Project/Cortex-analyzers/blob/master/LICENSE)
(Affero General Public License).

Responders are programs that perform different actions and apply to alerts, cases, tasks, task logs, and observables.

This document outlines the information needed to:

- install the Cortex analyzers and responders.
- update them when needed.
- configure them.

This documents also specifies whether the service that the analyzer is based
on is free or requires special access or valid subscription or product license.

## Table of Contents
 * [Introduction](#introduction)
  * [Free Analyzers](#free-analyzers)
    * [Abuse\_Finder](#abuse_finder)
    * [AbuseIPDB](#abuseipdb)
      * [Requirements](#requirements)
    * [Backscatter\.io](#backscatterio)
      * [Requirements](#requirements-1)
    * [C1fApp](#c1fapp)
      * [Requirements](#requirements-2)
    * [Censys\.io](#censysio)
      * [Requirements](#requirements-3)
    * [Clamav](#clamav)
    * [Crtsh](#crtsh)
    * [CuckooSandbox](#cuckoosandbox)
      * [Requirements](#requirements-4)
    * [CyberChef](#cyberchef) 
    * [Cybercrime\-Tracker](#cybercrime-tracker)
    * [Cyberprotect](#cyberprotect)
    * [Cymon](#cymon)
      * [Requirements](#requirements-5)
    * [DNSSinkhole](#dnssinkhole)
      * [Requirements](#requirements-6)
    * [DShield](#dshield)
    * [EmailRep](#emailrep)
    * [EmlParser](#emlparser)
    * [FileInfo](#fileinfo)
      * [Requirements](#requirements-7)
    * [FireHOLBlocklists](#fireholblocklists)
      * [Requirements](#requirements-8)
    * [Fortiguard](#fortiguard)
      * [Requirements](#requirements-9)
    * [GoogleDNS](#googledns)
    * [GoogleSafeBrowsing](#googlesafebrowsing)
      * [Requirements](#requirements-10)
    * [Hashdd](#hashdd)
      * [Requirements](#requirements-11)
    * [HIBP](#hibp)
      * [Requirements](#requirements-12)
    * [Hippocampe](#hippocampe)
      * [Requirements](#requirements-13)
    * [HybridAnalysis](#hybridanalysis)
      * [Requirements](#requirements-14)
    * [Hunterio\_DomainSearch](#hunterio_domainsearch)
      * [Requirements](#requirements-15)
    * [Maltiverse](#maltiverse)
    * [MalwareClustering](#malwareclustering)
    * [MaxMind](#maxmind)
    * [MISP](#misp)
      * [Requirements](#requirements-16)
    * [MISP Warninglists](#misp-warninglists)
      * [Requirements](#requirements-17)
    * [Msg\_Parser](#msg_parser)
    * [NSLR](#nslr)
      * [Requirements](#requirements-18)
    * [Onyphe](#onyphe)
      * [Requirements](#requirements-19)
    * [OpenCTI](#opencti)
      * [Requirements](#requirements-20)
    * [OTXQuery](#otxquery)
      * [Requirements](#requirements-21)
    * [Patrowl](#patrowl)
      * [Requirements](#requirements-22)
    * [PhishTank](#phishtank)
      * [Requirements](#requirements-23)
    * [PhishingInitiative](#phishinginitiative)
      * [Requirements](#requirements-24)
    * [Pulsedive](#pulsedive)
      * [Requirements](#requirements-25)
    * [Robtex](#robtex)
    * [SpamhausDBL](#spamhausdbl)
    * [StaxxSearch](#staxxsearch)
      * [Requirements](#requirements-26)
    * [StopForumSpam](#stopforumspam)
      * [Requirements](#requirements-27)
    * [Talos Reputation](#talos-reputation)
    * [Team Cymru MHR](#team-cymru-mhr)
    * [ThreatCrowd](#threatcrowd)
    * [Tor Blutmagie](#tor-blutmagie)
      * [Requirements](#requirements-28)
    * [Tor Project](#tor-project)
      * [Requirements](#requirements-29)
    * [Unshortenlink](#unshortenlink)
    * [UrlScan\.io](#urlscanio)
    * [URLhaus](#urlhaus)
    * [Virusshare](#virusshare)
      * [Requirements](#requirements-30)
    * [WOT](#wot)
      * [Requirements](#requirements-31)
    * [Yara](#yara)
      * [Requirements](#requirements-32)
    * [Yeti](#yeti)
      * [Requirements](#requirements-33)
  * [Analyzers Requiring Special Access](#analyzers-requiring-special-access)
    * [ANY.RUN](#anyrun)
    * [CERTatPassiveDNS](#certatpassivedns)
      * [Requirements](#requirements-34)
    * [CIRCLPassiveDNS](#circlpassivedns)
      * [Requirements](#requirements-35)
    * [CIRCLPassiveSSL](#circlpassivessl)
      * [Requirements](#requirements-36)
    * [GreyNoise](#greynoise)
      * [Requirements](#requirements-37)
    * [IBM X\-Force](#ibm-x-force)
      * [Requirements](#requirements-38)
    * [IPInfo](#ipinfo)
    * [IntezerCommunity](#intezercommunity)
    * [LastInfoSec](#lastinfosec)
    * [Malpedia](#malpedia)
      * [Requirements](#requirements-39)
    * [Malwares](#malwares)
      * [Requirements](#requirements-40)
    * [MalwareBazaar](#malwarebazaar)
    * [MnemonicPDNS](#mnemonicpdns)
      * [Requirements](#requirements-41)
    * [Sendgrid](#sendgrid)
    * [SinkDB](#sinkdb)
      * [Requirements](#requirements-42)
    * [Shodan](#shodan)
      * [Requirements](#requirements-43)
  * [Subscription and License\-based Analyzers](#subscription-and-license-based-analyzers)
    * [Autofocus](#autofocus)
      * [Requirements](#requirements-44)
    * [DNSDB](#dnsdb)
      * [Requirements](#requirements-45)
    * [DomainTools](#domaintools)
      * [Requirements](#requirements-46)
    * [DomainTools Iris](#domaintools-iris)
      * [Requirements](#requirements-47)
    * [EmergingThreats](#emergingthreats)
      * [Requirements](#requirements-48)
    * [FireEye iSIGHT](#fireeye-isight)
      * [Requirements](#requirements-49)
    * [JoeSandbox](#joesandbox)
      * [Requirements](#requirements-50)
    * [Investigate](#investigate)
      * [Requirements](#requirements-51)
    * [IPVoid](#ipvoid)
      * [Requirements](#requirements-52)
    * [Nessus](#nessus)
      * [Requirements](#requirements-53)
    * [PassiveTotal](#passivetotal)
      * [Requirements](#requirements-54)
    * [PayloadSecurity](#payloadsecurity)
      * [Requirements](#requirements-55)
    * [RecordedFuture](#recordedfuture)
      * [Requirements](#requirements-56)
    * [SecurityTrails](#securitytrails)
      * [Requirements](#requirements-57)
    * [SoltraEdge](#soltraedge)
      * [Requirements](#requirements-58)
    * [ThreatGrid](#threatgrid)
      * [Requirements](#requirements-59)
    * [Threat Response](#threat-response)
      * [Requirements](#requirements-60)
    * [Umbrella](#umbrella)
      * [Requirements](#requirements-61)
    * [VirusTotal](#virustotal)
      * [Requirements](#requirements-62)
    * [VMRay](#vmray)
      * [Requirements](#requirements-63)
  * [Free Responders](#free-responders)
    * [Redmine](#redmine)
      * [Requirements](#requirements-64)
    * [Wazuh](#wazuh)
      * [Requirements](#requirements-65)
    * [Palo Alto Minemeld](#palo-alto-minemeld)
      * [Requirements](#requirements-66)
  * [Subscription and License\-based Responders](#subscription-and-license-based-responders)
    * [AMP for Endpoints](#amp-for-endpoints)
      * [Requirements](#requirements-67)
    * [DomainTools Iris Malicious Tags](#domaintools-iris-malicious-tags)
    * [DomainTools Iris Risky DNS](#domaintools-iris-risky-dns)
    * [Crowdstrike Falcon](#crowdstrike-falcon)
      * [Requirements](#requirements-68)
    * [KnowBe4](#knowbe4)
      * [Requirements](#requirements-69)
    * [RT4-CreateTicket](#rt4-createticket)
    * [Sendgrid](#sendgrid) 
    * [Umbrella Blacklister](#umbrella-blacklister)
      * [Requirements](#requirements-70)
    * [VirusTotalDownloader](#virustotaldownloader)

## Introduction
All analyzer and responder configuration settings must be made using the Cortex Web UI. Please refer to the
 [Administration Guide](admin/admin-guide.md) for further details.

By default, and within every freshly created organization, all analyzers and responders are disabled. If you want to
 enable and configure them, use the Web UI (**Organization** > **Configurations** and **Organization** > **Analyzers** tabs).

## Free Analyzers

### Abuse_Finder
Use CERT-SG's [Abuse Finder](https://github.com/certsocietegenerale/abuse_finder)
to find abuse contacts associated with domain names, URLs, IPs and email addresses.

The analyzer comes in only one flavor.

No configuration is required. It can be used out of the box.

### AbuseIPDB

Get AbuseIPDB information related to an IP address.

This analyzer comes in only one flavor.

#### Requirements

This analyzer requires you to have an account on  [AbuseIPDB](https://www.abuseipdb.com/) and an API key.

To configure the analyzer you need to supply the key as a value of the `key` parameter.

### Backscatter.io

Brings observations and enrichment data from Backscatter.io scanning service.

This analyzer comes in 2 flavors:

- BackscatterIO_**GetObservations**: determine whether a value has a known scanning activity.
- BackscatterIO_**Enrichment**: enrich values.

#### Requirements

This analyzer requires you to have an account on [Backscatter.io](https://backscatter.io/) and an API key.

To configure the analyzer you need to supply the key as a value of the `key` parameter.

### C1fApp

Get [C1fApp](https://www.c1fapp.com/) information related to an IP address, a domain or a URL.

The analyzer comes in only one flavor.

#### Requirements
This analyzer requires you to have an account on [c1fapp.com](https://www.c1fapp.com/) and an API key.

To configure the analyzer you need to supply the key as a value of the `key` parameter.

### Censys.io
Get [Censys.io](https://censys.io) information about certificates using the associated IP, domain or hash.

The analyzer comes in only one flavor.

#### Requirements
Provide your API ID and the API secret as values for  `uid` and `key` parameters.

### Clamav
Clamav is a powerful and open source antivirus engine that allows writing your custom signature using Yara and sigtool
. This analyzer allows TheHive to communicate with a local clamav-daemon.

A detailed configuration guide is available on [Hetstat's website](https://laskowski-tech.com/2018/07/24/clamav-analyzer-for-thehive-and-cortex/).

The analyzer comes in only one flavor.

### Crtsh
Get [Crt.sh](https://crt.sh/) certificate transparency lists associated with a domain name. Crt.sh is an online service operated by the Comodo Certificate Authority.

The analyzer comes in only one flavor.

No configuration is required. It can be used out of the box.

### CuckooSandbox
Analyze URLs and files using [Cuckoo Sandbox](https://cuckoosandbox.org/).

The analyzer comes in two flavors:

- CuckooSandbox_**File_Analysis_Inet**: analyze files with Internet access.
- CuckooSandbox_**Url_Analysis**: analyze URLs.

#### Requirements
The CuckooSandbox analyzer requires you to have a local instance
 of Cuckoo Sandbox deployed. It is a FOSS that is free for use but needs to
 be manually deployed in your environment. Please go to
[https://cuckoosandbox.org/](https://cuckoosandbox.org/)
for more information on setting it up.

To configure the analyzer you need to supply the **API URL** of your local instance
as a value of the `url` parameter.

In addition, since Cuckoo 2.0.7, you need to specify an **API token** used for authentication.
This token can be found in your configuration, in the Cuckoo Working Directory (`$CWD/conf/cuckoo.conf`).

Finally, if you secured your API calls thanks to HTTPS, using a custom CA, you can specify it in
the `cert_path` parameter (`/etc/ssl/certs/my-custom-ca.pem`). Alternatively, you can disable TLS
certificate verification setting the `cert_check` parameter to false.


### CyberChef

[CyberChef](https://gchq.github.io/CyberChef/) is a simple, intuitive web app for carrying out all manner of “cyber” operations within a web browser. 

This analyzer connects to a CyberChef-server and comes in 3 flavors:

- **CyberChef_FromBase64**, that takes Base64 strings as input for CyberChef-server
- **CyberChef_FromCharCode**, that takes CharCode as input for CyberChef-server and run this recipe
- **CyberChef_FromHex** , that takes Hex strings as input for CyberChef-server

#### Requirements

Alll you need to run this analyzer,  is the URL to an instance of  [CyberChef-Server](https://github.com/gchq/CyberChef-server) as the value to the `url` parameter.

### Cybercrime-Tracker
Use the [Cybercrime-tracker.net](http://cybercrime-tracker.net/) service to assess whether an IP address, URL, domain, or FQDN has a C2 (Command & Control) entry in its database.

This analyzer comes in only one flavor.

No configuration is required. It can be used out of the box.

### Cyberprotect
Use the [Cyberprotect](https://threatscore.cyberprotect.fr/) ThreatScore service to get the cyber threat score of a domain or IP address, based on the Cyberprotect system.

This analyzer comes in only one flavor called **Cyberprotect_ThreatScore**.

No configuration is required. It can be used out of the box.

### Cymon
Checks IP addresses against [Cymon.io](https://cymon.io/).

This analyzer comes in only one flavor.

#### Requirements
You need to sign up to the service at [https://cymon.io/user/signup](https://cymon.io/user/signup). Once you do, provide your API key as the value to the `key` parameter.

### DNSSinkhole

Checks if an IP address is registered in your sinkhole. 

This analyzer comes in only one flavor.

#### Requirements
You need to provide the IP address of your sinkhole as the value of the`ip` parameter and the sinkholed IP address as the value of`sink_ip`.

### DShield
Checks IP addresses against SANS ISC [DShield](https://www.dshield.org/) database.

The analyzer comes in only one flavor called *DShield_lookup*.

No configuration is required. It can be used out of the box.

### EmailRep
Checks the reputation of an email address against the [emailrep.io](https://emailrep.io/) database.

This analyzer comes in only one flavor, and no specific configuration is required.

### EmlParser
Use the [eml_parser python library](https://github.com/GOVCERT-LU/eml_parser) to parse EML email and extract useful information.

No configuration is required. It can be used out of the box.

### FileInfo
Parse files in several formats such as OLE and OpenXML to detect VBA macros,
extract their source code, generate useful information on PE, PDF and Microsoft Office documents, Outlook `msg` files and much more.

The analyzer comes in only one flavor.

#### Requirements
Some configuration is required for the [Manalyze](https://github.com/JusticeRage/Manalyze) submodule.
This submodule needs to run binary program of Manalyze. There are two differents ways to do this:

- Compile binary program by following instructions on the [Github pages](https://github.com/JusticeRage/Manalyze). Then enable `manalyze_enable`, `manalyze_enable_binary` and specify `manalyze_binary_path` options in Cortex.
- Use docker on your Cortex server by setting up `manalyze_enable` and `manalyze_enable_docker` options in Cortex. The submodule program use the `evanowe/manalyze` container when running with Docker. The first analysis using this option could be long unless you first run `docker pull evanowe/manalyze` on your Cortex server.

### FireHOLBlocklists
Check IP addresses against the [FireHOL blocklists](https://firehol.org/).

The analyzer comes in only one flavor.

#### Requirements
This analyzer needs you to download the FireHOL block lists first to a
directory. Use `git` for that  purpose:
```commandline
$ mkdir /path/to/firehol
$ cd /path/to/firehol
$ git clone https://github.com/firehol/blocklist-ipsets
```

We advise you to keep the lists fresh  by  adding  a  cron  entry  to
regularly download them for example (using `git pull`).

Specify the directory where the lists have been downloaded using the
`blocklistpath` paramater and  an optional `ignoreolderthandays` parameter to
 ignore all lists that have not been updated in the last N days.

### Fortiguard
Check the [Fortiguard](https://fortiguard.com/webfilter) category of a URL or
 a domain.

The analyzer comes in only one flavor called *Fortiguard_URLCategory*.

#### Requirements
This anlyzer comes with a default configuration regarding categories and their maliciousness. If needed, this can be customized your own by [selecting the categories from the Fortiguard website](https://fortiguard.com/webfilter/categories). Select which categories you want to be considered malicious or suspicious, and others will be considered by the analyzer as info. Analyzed observables that are not categorised by Fortigard service is considered as safe.

### GoogleDNS
Query Google DNS information regarding a domain, FQDN or IP address.

The analyzer comes in only one flavor called *GoogleDNS*. No configuration is required. It can be used out of the box.

### GoogleSafeBrowsing
Check URLs against [Google Safebrowsing](https://www.google.com/transparencyreport/safebrowsing/).

The analyzer comes in only one flavor.

#### Requirements
You need to [obtain an API key](https://developers.google.com/safe-browsing/)
 from Google.

Provide your API key as a value of the `key` parameter.

### Hashdd
Check file hashes against the [Hashdd web service](https://www.hashdd.com/).

The analyzer comes in two flavors:
- **Status**: query hashdd without an API key for the threat level only.
- **Detail**: use an API key and obtain additional meta data about the sample.

#### Requirements
As long as you are using the **Status** flavor you don't need API key. If you want more details using the **Detail** flavor, you need to [sign up for a hashdd.com account and obtain an API](https://www.hashdd.com/).

### HIBP
Check email addresses against [Have I Been Pwned](https://haveibeenpwned.com).

The analyzer comes in only one flavor called **HIBP_Query**.

#### Requirements
The analyzer comes with an optional parameter to include unverified breaches within search results. If you do not want to include the optional parameter, the analyzer can be used out of the box.

### Hippocampe
Query threat feeds through [Hippocampe](https://github.com/TheHive-Project/Hippocampe),
a FOSS tool from TheHive Project that centralizes feeds and allows you to
associate a confidence level to each one of them (that can be changed over time)
 and get a score indicating the data quality.

The analyzer comes in two flavors:
- HippoMore: get the Hippocampe detailed report for an IP address, a domain or
 a URL.
- Hipposcore: get the Hippocampe Score report associated with an IP address, a
 domain or a URL.

#### Requirements
The Hippocampe analyzer requires you to have a local instance of Hippocampe
deployed/configured. It is a FOSS product that needs to be manually deployed
in your environment. Please go to [https://github.com/TheHive-Project/Hippocampe](https://github.com/TheHive-Project/Hippocampe)
 for more information on setting it up.

To configure the analyzer you need to supply the URL of your local instance
using the `url` parameter.

### HybridAnalysis
Fetch [Hybrid Analysis](https://www.hybrid-analysis.com/)
reports associated with hashes and filenames.

This analyzer comes in only one flavor called *HybridAnalysis_GetReport*.

#### Requirements
You need to have or create a free Hybrid Analysis [account](https://www.hybrid-analysis.com/signup).

Follow the instructions outlined on the [Hybrid Analysis API](https://www.hybrid-analysis.com/apikeys/info) page to generate
an API key/secret pair.

Provide the API key as a value for the `key` parameter and the secret as a
value to the `secret` parameter.

### Hunterio_DomainSearch
Query [https://hunter.io/](https://hunter.io/) and find emails associated with a given domain name.

This analyzer comes in only one flavor called *Hunterio_DomainSearch*.

#### Requirements
You need to have or create a free Hunter.io [account](https://hunter.io/). 

Provide the [API key](https://hunter.io/api_keys) as a value for the `key` parameter.

### Maltiverse
Query the free [Maltiverse](https://maltiverse.com/search) Threat Intelligence platform for enrichment information.

This analyzer comes in only one flavor, and no specific configuration is required.

### MalwareClustering
Get the latest malware report for a file, hash, domain or an IP address. Refer to Andrea Garavaglia's [presentation
](https://www.youtube.com/watch?v=nt2l5m8AJpQ) to learn more about this analyzer. In order to use it, you
 need to point it to a Neo4j server and provide:
- `n4j_host`: host address of the Neo4j server 
- `n4j_port`: port number of the Neo4j server 
- `n4j_user`: the Neo4j server username
- `n4j_pwd`:  the Neo4j server password
- `threshold`: ApiScout's correlation threshold

### MaxMind
Geolocate an IP Address via [MaxMind](https://www.maxmind.com/en/home)
GeoLite2 **free** City and Country databases.

Cortex does not refresh those databases automatically. It is up to you to
create a cron job to refresh them at the frequency you want. The files to
update are:

- `MaxMind/GeoLite2-City.mmdb`
- `MaxMind/GeoLite2-Country.mmdb`

You can fetch up-to-date versions from [https://dev.maxmind.com/geoip/geoip2/geolite2/](https://dev.maxmind.com/geoip/geoip2/geolite2/).

The analyzer comes in only one flavor.

No configuration is required. It can be used out of the box.

### MISP
Query multiple [MISP](http://www.misp-project.org/) (Malware Information
Sharing Platform) instances for events containing an observable.

[MISP](http://www.misp-project.org/) is a FOSS threat sharing
platform. It is considered the *de facto* standard in the field. You'd benefit
greatly from using it in conjunction to Cortex and TheHive as these 3
products make an interesting Threat Intelligence, Incident Response and
Digital Forensics ecosystem.

The analyzer comes in only one flavor.

#### Requirements
The MISP analyzer requires you to have access to one or several [MISP](http://www.misp-project.org/)
 instances. You can also deploy your own instance.

Four parameters are required to make the analyzer work:
- `url`
- `key`
- `certpath`
- `name`

You need the URL for each MISP instance you'd like to search. Those URLs go
in the `url` dict. You'll also need the authentication key associated with
your account on each of those instances. To obtain the key, log into the MISP
 instance's Web UI, click on your username on the top navigation bar and
 retrieve the value of the `Authkey` parameter. Each `Authkey` must be added,
  in the same order as the URLs to the `key` dict.

Another important parameter is the `certpath` dict. For each MISP instance:

- Use `false` if you don't want to validate the instance's X.509 certificate or
if the instance use old plain HTTP.
- Use `"/etc/ssl/certs"` or another file to validate the instance's X.509
certificate.

Last but not least, give each instance a name and add it in the order you
specified URLs and keys above to the `name` dict.

### MISP Warninglists
Check IP addresses, hashes, domains, FQDNs and URLs against [MISP WarningLists](https://github.com/MISP/misp-warninglists).

The analyzer comes in only one flavor.

#### Requirements
This analyzer needs you to download the MISP WarningLists first to a
directory. Use `git` for that  purpose:

```commandline
$ mkdir /path/to/misp-warninglists/repository
$ cd /path/to/misp-warninglists/repository
$ git clone https://github.com/MISP/misp-warninglists
```

We advise you to keep the lists fresh by adding a cron entry to regularly download them for example (using `git pull`).

Specify the directory where the WarningLists have been downloaded or updated using the
`path` paramater.

### Msg_Parser
Parse Outlook message files automatically and show the key information it
contains such as headers, attachments etc. Please note that the analyzer
doesn't extract attachments.

The analyzer comes in only one flavor.

No configuration is required. It can be used out of the box.

### NSLR
Check a hash or filename against [NSLR](https://www.nist.gov/itl/ssd/software-quality-group/national-software-reference-library-nsrl/nsrl-download) database, and ensure this is a good known one.

The analyzer comes in only one flavor.

#### Requirements
This analyzer needs you to download the and extract NSRLFile files from [NIST website](https://www.nist.gov/itl/ssd/software-quality-group/national-software-reference-library-nsrl/nsrl-download). 

- Set these files in a folder and configure it in the `nsrl_folder` parameter
- Add the path for grep command in parameter
- Alternatively you can import all these data in a postgresql database to improve response speed. If so, set the `conn` parameter

More information on how to set it up and install in [this detailed blog post](https://blog.thehive-project.org).

### Onyphe

Get publicly available information from [Onyphe](https://www.onyphe.io) for IP addresses.

The analyzer comes in five flavors:

- Onyphe_**Forward**: retrieve forward DNS lookup information Onyphe has for the given IPv{4,6} address with history of changes.
- Onyphe_**Geolocate**: retrieve geolocation information for the given IPv{4,6} address.
- Onyphe_**Ports**: retrieve SYN scan information Onyphe has for the given IPv{4,6} address with history of changes.
- Onyphe_**Reverse**: retrieve reverse DNS lookup information Onyphe has for the given IPv{4,6} address with history of changes.
- Onyphe_**Threats**: retrieve Onyphe threat information for the given IPv{4,6} address with history.
- Onyphe_**Inetnum**: retrieve Onyphe information about network information about an IPv4 or IPv6
- Onyphe_**Datascan**: retrieve Onyphe datascan information about a given IPv4, IPv6 or a any other string, with history of changes

#### Requirements
Provide the API key as a value for the `key` parameter.

### OpenCTI
Query multiple [OpenCTI](https://www.opencti.io/en/) instances for observables and reports containing them.

[OpenCTI](https://www.opencti.io/en/) is an open cyber threat intelligence platform which aims at providing a powerful knowledge management database with an enforced schema especially tailored for cyber threat intelligence and cyber operations and based on STIX 2.

The analyzer comes in two flavors to search for an observable in the platform:
 - OpenCTI_**SearchExactObservable**: returns an exact match only
 - OpenCTI_**SearchObservables**: returns all observables containing the input data

#### Requirements
The OpenCTI analyzer requires you to have access to one or several [OpenCTI](https://www.opencti.io/en/)
 instances in version 4. You can also deploy your own instance.

Three parameters are required for each instance to make the analyzer work:
- `url` # URL of the instance, e.g. "https://demo.opencti.io"
- `key` # API key of an account, e.g. "2b4f29e3-5ea8-4890-8cf5-a76f61f1e2b2"
- `name` # A custom name to distiguish beteen several instances, e.g. "Demo"

A global parameter `cert_check` is used to enable or disable TLS certificates verification.

### OTXQuery
Query AlienVault's [Open Threat Exchange](https://otx.alienvault.com/) for IPs,
domains, URLs, or file hashes.

The analyzer comes in only one flavor.

#### Requirements
You need to sign up for an [OTX](https://otx.alienvault.com/) account or use
an existing one.

Log in to your OTX account, click on your username on the top
navigation bar then on *Settings* and retrieve your OTX key and use it as the
value of the `key` parameter.

### Patrowl
Get the current [Patrowl](https://github.com/Patrowl/PatrowlManager) report for a fdqn, a domain or an IP address.

The analyzer comes in only one flavor called **Patrowl_GetReport**.

#### Requirements
You need a running [Patrowl](https://github.com/Patrowl/PatrowlManager) instance or to have access to one to use the analyzer. Supply the following parameters to the analyzer in order to use it:

- `url`: The PatrowlManager service URL
- `api_key`: A valid API Key of a Patrowl user

### PhishTank
Query [PhishTank](https://www.phishtank.com/) to assess whether a URL has
been flagged as a phishing site.

The analyzer comes in only one flavor called *PhishTank_CheckURL*.

#### Requirements
You need to sign up for a [PhishTank](https://www.phishtank.com/register.php)
 account or use an existing one.

Log in to your PhishTank account, click on the *Developers* tab then on
*Manage Applications*, register an application by giving it a name and
entering a CAPTCHA code. You'll obtain an API key that you'll need to supply
as the value to the `key` configuration parameter for this analyzer to work.

### PhishingInitiative
Query [Phishing Initiative](https://phishing-initiative.fr/contrib/) to
assess whether a URL has been flagged as a phishing site.

This analyzer comes in two flavors called *PhishingInitiative_Lookup* and *PhishingInitiative_Scan*.

#### Requirements
You need to sign up for a [Phishing Initiative](https://phishing-initiative.fr/register)
 account or use an existing one.

Log in to your Phishing Initiative account, click on the icon representing
your account details then on *API*. Retrieve the API key value and supply
it as the value to the `key` configuration parameter.

### Pulsedive
Query [Pulsedive](https://pulsedive.com/) and get information about a domain name,  hash, IP or URL.

This analyzer comes in only one flavor called *Pulsedive_GetIndicator*.

#### Requirements
You need to sign up for a [Pulsedive](https://pulsedive.com/)
 account or use an existing one.

Provide the [API key](https://pulsedive.com/api/) as a value for the `key` parameter.

### Robtex
Query the [Robtex](https://www.robtex.com/) database and retrieve information about a domain, a FQDN or an IP address.

This analyzer comes in three flavors:

- Robtex_**Forward_PDNS_Query**: check domains/FQDNs using the Robtex passive DNS database.
- Robtex_**IP_Query**: make IP lookups against the Robtex DB.
- Robtex_**Reverse_PDNS_Query**: check IPs in Robtex reverse passive DNS database.

The analyzer uses the free Robtex API which needs no subsequent configuration. However, the free API has limits with regard to rates and amount of data returned.

### SpamhausDBL
This analyzer queries the [Spamhaus Domain Block List (DBL)](https://www.spamhaus.org/dbl/), which provides domain
 reputation information.

This analyzer comes in only one flavor, `DBLLookup`.

No configuration is needed. It can be used out of the box.

### StaxxSearch
Fetch observable details from an [Anomali STAXX](https://www.anomali.com/platform/staxx) instance.

This analyzer comes in only one flavor.

#### Requirements
You need to install an Anomali STAXX instance or to have access to one to use the analyzer. Supply the following parameters to the analyzer in order to use it:

- `auth_url`: URL of the authentication endpoint.
- `query_url`: URL of the intelligence endpoint.
- `username`: the STAXX user name.
- `password`: the STAXX password.
- `cert_check`: boolean indicating whether the certificate of the endpoint must be checked or not.
- `cert_path`: path to the CA on the system to validate the endpoint's certificate if `cert_check` is true.

### StopForumSpam
Query [StopForumSpam](http://www.stopforumspam.com) to check if an IP or email address is a known spammer.

#### Requirements
You need to define the thresholds above which the analyzed observable should be marked as `suspicious` or `malicious`.

### Talos Reputation
This analyzer lets you determine whether an IP address has been reported as a threat on [Cisco Talos Intelligence](https://talosintelligence.com/) service. No special access to is required to run the analyzer.

This analyzer comes in only one flavor.

No configuration is needed. It can be used out of the box.

### Team Cymru MHR 
This analyzer allows you to submit a file hash to [Team Cymru's Malware Hash Registry](https://www.team-cymru.com/mhr.html), and return an evaluation (detection percentage).

The analyzer comes in only one flavor called `HashLookup`.

No configuration is required. It can be used out of the box.

### ThreatCrowd
Look up domains, mail and IP addresses on [ThreatCrowd](https://www.threatcrowd.org/), a service powered by AlienVault.

This analyzer comes in only one flavor.

No configuration is needed. It can be used out of the box.

### Tor Blutmagie
Check if an IP address, a domain or a FQDN is known by [Blutmagie](http://torstatus.blutmagie.de/) to be linked to a Tor node.

This analyzer comes in only one flavor.

#### Requirements
In order to check if an IP, domain or FQDN is a Tor exit node, this analyzer queries the Tor status service at Blutmagie.de. The analyzer uses a caching mechanism in order to save some time when doing multiple queries, so the configuration includes parameter regarding the cache directory and the duration of caching.

### Tor Project
Check if an IP address is known to be a Tor node. The information source is the official Tor network status.

This analyzer comes in only one flavor.

#### Requirements
The analyzer uses a caching mechanism in order to save some time when doing multiple queries, so the configuration includes parameter regarding the cache directory and the duration of caching. This analyzer also accepts a `ttl` parameter, which is the threshold in seconds for exit nodes before they get discarded.

### Unshortenlink
Follow redirects of shortened URLs to reveal the real ones.

This analyzer comes in only one flavor.

No configuration is required. It can be used out of the box.

**Warning**: using this analyzer without **extra caution** might lead to unexpected consequences. For example, if the URL you are seeking to unshorten is an attacker-controlled one, you may end up leaving undesired traces in the threat actor's infrastructure logs. The TLP values Cortex allows you to configure to prevent the use of an analyzer if the TLP associated with an observable is above the authorized level won't be of much help since Unshortenlink have to access the shortened URL. Please do not activate this analyzer unless you (and your fellow analysts) know what they are doing.

### UrlScan.io
Search IPs, domains, hashes or URLs on [urlscan.io](https://urlscan.io).

This analyzer comes in only one flavor.

No configuration is required. It can be used out of the box.

### URLhaus
Check if a domain, URL or hash is known by Abuse.ch and stored in the [URLhaus](https://urlhaus.abuse.ch/) database, and get a report about its 'maliciousness'.

This analyzer comes in only one flavor.

No configuration is needed. It can be used out of the box.

### Virusshare
Check whether a file or hash is available on [VirusShare.com](https://virusshare.com/).

This analyzer comes in only one flavor.

#### Requirements
Prior to using the analyzer, you need to retrieve the Virusshare hash lists
using the `download_hashes.py` script that is located in the same directory
as the analyzer. To keep your lists fresh, you may want to regularly
download them using a cron entry or a similar system.

Indicate the path where you have downloaded the hash lists using the `path`
parameter.

### WOT
Check a domain against [Web of Trust](https://www.mywot.com/), a website
reputation service.

This analyzer comes in only one flavor called *WOT_Lookup*.

#### Requirements
An account with Web of Trust is required to get an API key, which is
necessary to configure the analyzer. You can sign up for an account at
[https://www.mywot.com/en/signup?destination=profile/api](https://www.mywot.com/en/signup?destination=profile/api).

Supply the API key you'll find under [https://www.mywot.com/en/signup?destination=profile/api](https://www.mywot.com/en/signup?destination=profile/api)
 as the value for the `key` parameter.

### Yara
Check files against [YARA](https://virustotal.github.io/yara/) rules using
[yara-python](https://github.com/VirusTotal/yara-python).

The analyzer comes in only one flavor.

#### Requirements
You need to point your analyzer to multiple files and/or directories
containing your YARA rules. If you supply a directory, the analyzer expects to
find an *index.yar* or *index.yas* file. The index file can include other rule
files. An example can be found in the [Yara-rules](https://github.com/Yara-Rules/rules/blob/master/index.yar)
repository.

Add each file and/or directory containing YARA rules to the `rules` dict.

### Yeti
[YETI](https://yeti-platform.github.io/) is a FOSS platform meant to organize
 observables, indicators of compromise, TTPs, and knowledge on threats in a
 single, unified repository. The analyzer for this platform lets you make API
 calls to YETI and retrieve all available information pertaining to a domain,
 a fully qualified domain name, an IP address, a URL or a hash.

This analyzer comes in only one flavor.

#### Requirements
The Yeti analyzer requires you to have a local instance of [YETI](https://yeti-platform.github.io/)
deployed/configured. It is an open source tool that is free for use but needs
 to be manually deployed in your environment.

Provide the URL of your YETI instance as a value for the `url` parameter. Yhe analyzer also allow you to use an API key if needed.

## Analyzers Requiring Special Access

### Any.run

ANY.RUN is a malware sandbox service in the cloud. 

This analyzer comes in only one flavor. Submit a `file` or `url` to the service for analysis, and get a report.

#### Requirements

you need to [register](https://app.any.run) and provide yout API key as a value of `token` to use this analyzer.

### CERTatPassiveDNS
Check CERT.at Passive DNS Service for a given domain.

This analyzer comes in only one flavor.

#### Requirements
Access to the CERT.at service is allowed to trusted partners only. If you
think you qualify, please contact [CERT.at](http://www.cert.at/index_en.html).

No configuration is required. It can be used out of the box if CERT.at positively answers your access request.

### CIRCLPassiveDNS
Check [CIRCL's Passive DNS](https://www.circl.lu/services/passive-dns/) for a
 given domain.

This analyzer comes in only one flavor.

#### Requirements
Access to CIRCL Passive DNS is only allowed to trusted partners in Luxembourg
and abroad. [Contact CIRCL](https://www.circl.lu/contact/) if you would like
access. Include your affiliation and the foreseen use of the Passive DNS
data.

If the CIRCL positively answers your access request, you'll obtain a username
 and password which are needed to make the analyzer work.

supply your username as the value for the `user` parameter and your password
as the value for the `password` parameter.

### CIRCLPassiveSSL
Check [CIRCL's Passive SSL](https://www.circl.lu/services/passive-ssl/)
service for a given IP address or certificate hash.

This analyzer comes in only one flavor.

#### Requirements
Access to CIRCL Passive SSL is allowed to partners including security
researchers or incident analysts worldwide. [Contact CIRCL](https://www.circl.lu/contact/)
if you would like access.

If the CIRCL positively answers your access request, you'll obtain a username
 and password which are needed to make the analyzer work.

Supply your username as the value for the `user` parameter and your password
as the value for the `password` parameter.

### GreyNoise
Determine whether an IP has known scanning activity using [GreyNoise](https://greynoise.io/).

This analyzer comes in only one flavor.

#### Requirements
The analyzer can be used out of the box without configuration. However, if you make many requests, you need to obtain an API key. Please [contact GreyNoise](https://greynoise.io/contact/) to ask for one.

Once you get the API key, provide it as the value of the `key` parameter.

### IBM X-Force
Query domains, IPs, hashes and URLs against [IBM X-Force](https://exchange.xforce.ibmcloud.com/) Threat Intelligence sharing platform.

This analyzer comes in only one flavor.

#### Requirements
Access to IBM X-Force Threat Exchange requires an [IBM ID](https://www.ibm.com/account/reg/signup?formid=urx-30243).

Once you have access to the service, supply the URL of the service as value for the `url` parameter, the API key associated with your account as value for the `key` parameter and the associated password as the value of the `pwd` parameter.

### IPInfo
Get enrichment information from the [ipinfo.io](ipinfo.io) service. This analyzer comes in two flavors: 
- IPinfo_**Details** : IPinfo details lookup.
- IPinfo_**Hosted_Domains** : Host domain lookup.

To query the service, you need to provide an API key as value of the `api_key` parameter. 

### IntezerCommunity
Submit a file to [Intezer Analyze™](http://intezer.com/intezer-analyze/) and get an analysis report. This analyzer comes in only one flavor.

To query the service, you need to provide an API key as value of the `key` parameter. 

### LastInfoSec
Gather intelligencefor a `hash` or a `domain` from [LastInfoSec](https://www.lastinfosec.com/) services. 

This analyzer comes in only 1 flavor. 

#### Requirements
To query the service, you need to provide an API key as value of the `apiKey` parameter. 

### Malpedia
Scan files against YARA rules automatically downloaded every 10 hours by the analyzer from [Malpedia](https://malpedia.caad.fkie.fraunhofer.de/).

If a rule matches, the analyzer tries to retrieve more info from Malpedia such as the malware family (currently more than 600) and the actor group (tracked through [MISP Galaxies](https://github.com/MISP/misp-galaxy)).

This analyzer comes in only one flavor.

#### Requirements
You need access to Malpedia to use this analyzer. Please note that Malpedia does not feature open registration. It is operated as an invite-only trust group. If you believe you qualify for an account, please see Malpedia's [Terms of Services](https://malpedia.caad.fkie.fraunhofer.de/terms_of_service) for contact details.

If you have access to Malpedia, provide your username as the value for the `username` parameter and the associated password as the value of the `password` parameter then specify a location where the analyzer will download the YARA rules to using the `path` parameter.

### Malwares
Query [Malwares.com](https://www.malwares.com/) and get reports on files, hashes, domain names and IP addresses.

The analyzer comes in two flavors:
- Malwares_**GetReport**: get the latest Malwares report for a file,
hash, domain or an IP address.
- Malwares_**Scan**: scan a file or URL.

#### Requirements
You need to [sign up](https://www.malwares.com/account/signup) for a Malwares.com account.

An API key to use the service's API should be associated with your account. Supply it as the value of the `key` parameter.

### MalwareBazaar
MalwareBazaar is a project from [abuse.ch](https://bazaar.abuse.ch/) with the goal of sharing malware samples with the infosec community, AV vendors and threat intelligence providers. 

The analyzer comes in one flavor and let you gather information from `domain`, `fqdn`, `hash`, `url` and `ip`.

#### Requirements
You need to login to MalwareBazaar with your Twitter account to obtain an API key and use is as the value of  `api_key` parameter in Cortex configuration. 


### MnemonicPDNS
Query IP addresses and domain names against [Mnemonic](https://passivedns.mnemonic.no/) Passive DNS service.

This analyzer comes in two flavors:
- Mnemonic_pDNS_**Public**: query Mnemonic's public service.
- Mnemonic_pDNS_**Closed**: query Mnemonic's closed service.

#### Requirements
When using the public service, the analyzer can be used out of the box with no further configuration.

When using the closed service, you need to contact Mnemonic to get an API key which you'll need to supply as the value of the `key` parameter.

### SinkDB
Check SinkDB service from [abuse.ch](https://abuse.ch) fort a given IP address.

This analyzer comes in only one flavor.

#### Requirements
SinkDB is a private service provided by [abuse.ch](https://abuse.ch) which collects sinkholed IPs.
Access to this service is restricted to trusted partners. Request an access using the form available on the [SinkDB website](https://sinkdb.abuse.ch/) if you would like access.

Provide the API key as a value for the `key` parameter.

### Shodan
Retrieve key [Shodan](https://www.shodan.io/) information on domains and IP addresses.

This analyzer comes in 6 flavors:
- Shodan_**Host**: get Shodan information on a host.
- Shodan_**Search**: get Shodan search result on a domain.
- Shodan_**DNSResolve**: get Shodan domain resolutions.
- Shodan_**Host_History**: get Shodan history scan results for an IP.
- Shodan_**InfoDomain**: get Shodan information on a domain.
- Shodan_**ReverseDNS**: get Shodan reverse DNS resolutions on an IP.

#### Requirements
You need to create a Shodan account and retrieve the associated API Key. For
best results, it is advised to get a [Membership](https://enterprise.shodan.io/product-comparison)
level account, otherwise a free one can be used.

Supply the API key as the value for the `key` parameter.

## Subscription and License-based Analyzers

### Autofocus
[Autofocus](https://www.paloaltonetworks.com/cortex/autofocus) is a commercial Threat Intelligence Platform provided by
 Palo Alto Networks.

This analyzer comes in 3 flavors:
- AUTOFOCUS_**GetSampleAnalysis**: fetch the full analysis of a sample based on its hash.
- AUTOFOCUS_**SearchIOC**: fetch samples linked to a specific IoC (domain, fqdn, user-agent, imphash, ip, mutex, url
, tag). Please note that mutex and tag are not default datatypes in TheHive. You need to create them in TheHive before
 you can
 leverage
 them. 
- AUTOFOCUS_**SearchJSON**: fetch samples matching a complex search in JSON format (other).

#### Requirements
You need to be an Autofocus customer of Palo Alto Networks to have access to their API and be able to use the analyzer.

Provide your API key as the value of the `apikey` parameter.

### DNSDB
Leverage Farsight Security's [DNSDB](https://www.dnsdb.info/) for Passive DNS.

This analyzer comes in three flavors:
- DNSDB_**DomainName**: fetch historical records for a domain.
- DNSDB_**IPHistory**: fetch historical records for an IP address.
- DNSDB_**NameHistory**: fetch historical records for a fully-qualified domain
 name.

#### Requirements
You need a [valid subscription](https://www.farsightsecurity.com/order-services/)
to Farsight Security's DNSDB service to use the analyzer.

Provide the URL of the DNSDB API service to the `server` parameter. The
default (`https://api.dnsdb.info`) should work. If it doesn't, contact
Farsight Security.

Provide your API key as a value to the `key` parameter.

### DomainTools
Look up domain names, IP addresses, WHOIS records, etc. using the popular
[DomainTools](http://domaintools.com/) service API.

The analyzer comes in 10 flavors:
- DomainTools_**HostingHistory**: get a list of historical registrant, name servers and IP addresses for a domain.
- DomainTools_**ReverseIP**: get a list of domain names sharing the same IP address.
- DomainTools_**ReverseIPWhois**: get a list of IP addresses which share the same registrant information. It applies to a mail, IP, or domain.
- DomainTools_**ReverseNameServer**: get a list of domain names that share the same primary or secondary name server.
- DomainTools_**ReverseWhois**: get a list of domain names which share the same registrant information.
- DomainTools_**WhoisHistory**: get a list of historical Whois records associated with a domain name.
- DomainTools_**WhoisLookup**: get the ownership record for a domain or IP address with basic registration details parsed.
- DomainTools_**WhoisLookupUnparsed**: get the ownership record for an IP address or domain with basic registration details without parsing.
- DomainTools_**Risk**: get a risk score for a given domain name.
- DomainTools_**Reputation**: get a reputation score for a given domain name.

#### Requirements
You need a [valid DomainTools API integration subscription](https://www.domaintools.com/products/api-integration/)
to use the analyzer.

Provide your username as a value for the `username` parameter and API key as
a value for the `key` parameter.

### DomainTools Iris
 Look up domain names, IP addresses, e-mail addresses, and SSL hashes using the popular
 [DomainTools Iris](https://www.domaintools.com/resources/api-documentation/iris-investigate/) service API.

 The analyzer comes in 2 flavors:
 - DomainToolsIris_**Investigate**: Use DomainTools Iris API to investigate a domain.
 - DomainToolsIris_**Pivot**: Use DomainTools Iris API to pivot on ssl_hash, ip, or email.

 #### Requirements
 You need a [valid DomainTools API integration subscription](https://www.domaintools.com/products/api-integration/) to use the analyzer:

- Provide your username as a value for the `username` parameter and API key as
 a value for the `key` parameter.
- Set the `pivot_count_threshold` parameter to highlight any item below that value as being of interest in the
 report's template.


### EmergingThreats
Leverage Proofpoint's [Emerging Threats Intelligence](https://threatintel.proofpoint.com/)
to assess the reputation of various observables and obtain additional and
valuable information on malware.

The service comes in three flavors:
- EmergingThreats_**DomainInfo**: retrieve ET reputation, related malware, and IDS requests for a given domain.
- EmergingThreats_**IPInfo**: retrieve ET reputation, related malware, and IDS requests for a given IP address.
- EmergingThreats_**MalwareInfo**: retrieve ET details and info related to a malware hash.

#### Requirements
You need a valid Proofpoint [Emerging Threats Intelligence](https://www.proofpoint.com/us/products/et-intelligence)
subscription to use the analyzer.

Retrieve the API key associated with your account and provide it as a value
to the `key` parameter.

### FireEye iSIGHT
Leverage FireEye [iSIGHT Threat Intelligence](https://www.fireeye.com/solutions/isight-cyber-threat-intelligence-subscriptions.html)
to qualify domains, IP addresses, hashes and URLs.

This analyzer comes in only one flavor.

#### Requirements
You need a valid FireEye [iSIGHT Threat Intelligence](https://www.fireeye.com/solutions/isight-cyber-threat-intelligence-subscriptions.html)
subscription to use the analyzer.

Retrieve the API key associated with your account and provide it as a value
to the `key` parameter. Obtain the password associated with the API key and provide it as a value to the `pwd` parameter.

### JoeSandbox
Analyze URLs and files using the powerful [Joe Sandbox](https://www.joesecurity.org/)
malware analysis solution.

Joe Sandbox is a commercial solution by Joe Security LLC. It comes in several
versions. The analyzer has been tested with [Joe Sandbox Cloud](https://www.joesecurity.org/joe-sandbox-cloud),
[Joe Sandbox Ultimate](https://www.joesecurity.org/joe-sandbox-ultimate) and
[Joe Sandbox Complete](https://www.joesecurity.org/joe-sandbox-complete).

The analyzer comes in 3 flavors:
- JoeSandbox_**File_Analysis_Inet**: analyze files while providing Internet
access.
- JoeSandbox_**File_Analysis_Noinet**: analyze files without providing
Internet access.
- JoeSandbox_**Url_Analysis**: analyze URLs.

#### Requirements
Provide the URL of your on-premises Joe Sandbox instance or the cloud version
 to the `url` parameter and supply the associated API key as a value for the
 `key` parameter.

### Investigate
Leverage Cisco [Umbrella Investigate](https://umbrella.cisco.com/) to qualify a domain, a FQDN or a hash.

The analyzer comes in 2 flavors:
- Investigate_**Categorization**: analyze domain or FQDN
- Investigate_**Sample**: analyze a hash.

#### Requirements
Retrieve the API key associated with your account and provide it as a value for the `key` parameter.

### IPVoid

This analyzer leverages the IP reputation check on [apivoid.com](https://apivoid.com), the API of the
 [ipvoid.com](https://ipvoid.com) web service. 

This analyzer comes in only one flavor.

#### Requirements
Retrieve the API key associated with your account on [apivoid.com](https://apivoid.com) and provide it as a value for the `key` parameter. 


### Nessus
Use [Nessus Professional](https://www.tenable.com/products/nessus-vulnerability-scanner),
a popular vulnerability scanner to scan an IP address or a FQDN. This analyzer works with Nessus 6 or earlier. Tenable has [removed API access](https://www.tenable.com/blog/a-clarification-about-nessus-professional) starting from version 7 rendering this analyzer useless with that version.

The analyzer comes in only one flavor.

#### Requirements
You must have a locally deployed instance of Nessus Professional 6 or earlier to use the
analyzer. The scanner must have at least a scan policy defined. You must not
scan assets that do not belong to you, unless you really know what you are
doing. That’s why safeguards were built in the analyzer’s configuration.

To configure the analyzer, you must supply four parameters:
- `url`: URL of your Nessus scanner.
- `login`: username to log to the scanner.
- `password`: password of your login account.
- `policy`: the scan policy to use.
- `ca_bundle`: an optional parameter to validate the X.509 certificate of the
 scanner. This parameter **must be omitted if no validation is needed**.
- `allowed_networks`: a list of networks in CIDR notation that the scanner is
 allowed to probe.

### PassiveTotal
Leverage RiskIQ's [PassiveTotal service](https://www.passivetotal.org/) to
gain invaluable insight on observables, identify overlapping infrastructure
using Passive DNS, WHOIS, SSL certificates and more.

The analyzer comes in 8 flavors:
- PassiveTotal_**Enrichment**: enrichment Lookup.
- PassiveTotal_**Malware**: malware Lookup.
- PassiveTotal_**Osint**: OSINT Lookup.
- PassiveTotal_**Passive_Dns**: passive DNS Lookup.
- PassiveTotal_**Ssl_Certificate_Details**: SSL Certificate Details.
- PassiveTotal_**Ssl_Certificate_History**: Ssl Certificate History Lookup.
- PassiveTotal_**Unique_Resolutions**: Unique Resolutions Lookup.
- PassiveTotal_**Whois_Details**: Whois Details Lookup.
- PassiveTotal_**Trackers**: tracker lookups.
- PassiveTotal_**Host_Pairs**: host pair lookups.
- PassiveTotal_**Components**: Component lookups.

#### Requirements
You need a PassiveTotal account to obtain the API key which is required to
use the analyzer. If you sign up for a [Community Edition Account](https://community.riskiq.com/registration),
you'll have a very limited number of queries. You can purchase a
[PassiveTotal subscription](https://www.riskiq.com/contact/) for a higher
number of queries per day.

Provide your account's username as the value of the `username` parameter and
the associated API key as value for the `key` parameter.

### PayloadSecurity
Submit files or URLs to an on premise [PayloadSecurity](https://www.payload-security.com/) sandbox and fetch the associated reports.

This analyzer comes in only one flavor.

#### Requirements
Five parameters are required to make the analyzer work:
- `url`
- `key`
- `secret`
- `environmentid`
- `verifyssl`

Provide the API key as a value for the `key` parameter and the secret as a
value to the `secret` parameter. the `url` parameter should be the address of your on premise service. `environmentid` should also be gathered from your custom configuration.

### RecordedFuture
Get the latest risk data from [RecordedFuture](https://www.recordedfuture.com/) for a hash, domain or an IP address.

This analyzer comes in only one flavor **RecordedFuture**.

#### Requirements
Retrieve the API key associated with your account and provide it as a value for the `key` parameter.

### SecurityTrails
Get Whois and Passive DNS details using [SecurityTrails](https://securitytrails.com/).

The analyzer comes in 2 flavors:
- SecurityTrails_**Passive_Dns**: Passive DNS Lookup.
- SecurityTrails_**Whois**: Whois Details Lookup.

#### Requirements
You need a SecurityTrails account to obtain the API key which is required to use the analyzer.

Provide your account's API Key as the value of the `api_key` parameter.

### SoltraEdge

Get information about any observable dataType from a SoltraEdge server. 

#### Requirements

An account an a token from a SoltraEdge server are required to use this analyzer. Provide this information as values of `account`,`token` and `base_url` parameters. 

### ThreatGrid
The [Cisco Threat Grid](https://cisco.com/go/threatgrid) analyzer has the following features:
- Submit a `file` for analysis
- Submit a `url` for analysis
- Query Threat Grid for a `hash` (MD5, SHA1, SHA256) and get the highest scoring analysis results
- Pivot into Threat Grid report to view the analysis
- Pivot into Threat Grid report to a specific Behavioral Indicator
- Pivot into Threat Grid report to a specific TCP/IP Stream

The analyzer comes in only one flavor.

#### Requirements
You must have a Cisco Threat Grid Premium account with API access.

To configure the analyzer, you must supply two parameters:
- `tg_host`: hostname of your Threat Grid instance.
- `api_key`: API key to authenticate to your Threat Grid instance.

### Threat Response
The [Cisco Threat Response](https://cisco.com/go/threatresponse) analyzer has the following features:
- Query Threat Response for Verdicts and Sightings for:
    - `domain`
    - `filename`
    - `fqdn`
    - `hash` (MD5, SHA1, SHA256)
    - `ip`
    - `url`
- Pivot into a Threat Response investigation of an observable
- If the AMP for Endpoints module is configured in Threat Response and the feature is enabled on the analyzer: when a
 target is returned from the AMP for Endpoints module the analyzer will extract the connector GUIDs as Artifacts to
  enable seamless use of the [AMP for Endpoints Responder](#amp-for-endpoints).

The analyzer comes in only one flavor.

#### Requirements
You must have a Cisco Threat Response account and a Threat Response API Client ID with the `enrich` scope.

To configure the analyzer, you must supply three parameters:
- `region`: The Threat Response region your account is in (US, EU, APJC).
- `client_id`: Threat Response API Client ID with appropriate scopes.
- `client_password`: Password for the Threat Response API Client.

### Umbrella

Query the [Umbrella Reporting API](https://docs.umbrella.com/umbrella-api/docs/reporting-destinations-most-recent-requests) for recent DNS queries and their status, for a domain.

This analyzer comes in only one flavor **Umbrella_Report**.

#### Requirements
Four parameters are required to make the analyzer work:
- `api_key`
- `api_secret`
- `organization_id`
- `query_limit`, defaults to 20

Provide the API key as a value for the `api_key` parameter and the secret as a value to the `api_secret` parameter. The `organization_id` parameter should be provided by the Umbrella Admin Console. `query_limit` is optional, and represents the maximum number of results to return.

### VirusTotal
Look up files, URLs and hashes in [VirusTotal](https://www.virustotal.com/).

The analyzer comes in two flavors:
- VirusTotal_**GetReport**: get the latest VirusTotal report for a file,
hash, domain, URL or an IP address.
- VirusTotal_**Scan**: scan a file or URL.

#### Requirements
You need a [VirusTotal](https://www.virustotal.com/#/join-us) community
account or a [Private API](https://support.virustotal.com/hc/en-us/requests/new)
subscription, a premium service.

Please note that a community account is highly limited in the number of API
queries it can make. If you can afford them, subscribe to the premium services.

Provide the API key associated with your account as a value to the `key`
parameter.

### VMRay
Analyze files using the [VMRay Analyzer Platform](https://www.vmray.com/products/)
commercial sandbox.

The analyzer comes in only one flavor. It lets you run a file in a local or
remote (cloud) VMRay sandbox. The analyzer also lets you check existing
analysis reports.

#### Requirements
You need a VMRay Analyzer Platform to use the analyzer.

To configure the analyzer, provide the URL of the platform as a value for the
`url` parameter and the API key as a value for the `key` parameter.

To validate the X.509 certificate of your VMRay Analyzer Platform instance,
use the `certpath` parameter.

## Free Responders

### Redmine

This responder can be used to create an issue in the Redmine ticketing system from a case. It will use the case title as the issue subject and the case description as the issue body. 

To set it up in Cortex, you will need:
- To define a user to allow Cortex to connect to Redmine and with access to the various projects in which issues should
 be created
- Define three custom fields in TheHive that will be used to select the project, the tracker and, optionally, the
 assignee of the issue. These fields can be free form or can be custom fields with preset values.

#### Requirements
The following options are required in the Redmin Responder configuration: 

- `instance_name`: Name of the Redmine instance
- `url`: URL where to find the Redmine API
- `username`: Username to log into Redmine
- `password`: Password to log into Redmine
- `project_field`: Name of the custom field containing the Redmine project to use when creating the issue
- `tracker_field`: Name of the custom field containing the Redmine tracker to use when creating the issue
- `assignee_field`: Name of the custom field containing the Redmine assignee to use when creating the issue
- `reference_field`: Name of the case custom field in which to store the opened issue. If not defined, this information will not be stored
- `opening_status`: Status used when opening a Redmine issue (if not defined here, the responder will use the default
 opening status from the Redmine Workflow)
- `closing_task`: Closing the task after successfully creating the Redmine issue


### Wazuh
This responder performs actions on [Wazuh](https://wazuh.com/), the open source security monitoring platform. It
 currently supports ad-hoc firewall blocking of ip observables.

The responder will use the provided `wazuh_agent_id` (custom field) to call back to the defined Wazuh manager to
 implement the active response action.

#### Requirements
The following options are required in the Wazuh Responder configuration:   

- `wazuh_manager`: The Wazuh manager API address/port    
- `wazuh_user`:  The Wazuh API user    
- `wazuh_password`: The Wazuh API password   

The following custom fields should be created and populated in related records:   

- `wazuh_agent_id`: The ID of the Wazuh agent that witnessed activity to generate the alert 
- `wazuh_alert_id`: The Wazuh alert ID generated by the Wazuh manager   
- `wazuh_rule_id`: The rule ID associated with the Wazuh alert   

### Palo Alto Minemeld
This responder sends observables you select to a [Palo Alto Minemeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld) instance.

#### Requirements
The following options are required in the Palo Alto Minemeld Responder configuration:

- `minemeld_url` : URL of the Minemeld instance to which you will be posting indicators   
- `minemeld_user`: user accessing the Minemeld instance
- `minemeld_password`:  password for the user accessing the Minemeld instance
- `minemeld_indicator_list`: name of Minemeld indicator list (already created in Minemeld)
- `minemeld_share_level`: share level for indicators (defaults to `red`)
- `minemeld_confidence`: confidence level for indicators (defaults to `100`)
- `minemeld_ttl`: TTL for indicators (defaults to `86400` seconds)

## Subscription and License-based Responders

### AMP for Endpoints
The [Cisco AMP for Endpoints](https://cisco.com/go/ampendpoints) responder has the following features:
- Add a SHA256 to a Simple Custom Detection List
    - TheHive's case ID and description are appended to the description
- Remove a SHA256 from a Simple Custom Detection List
- Move a connector GUID to a new group
- Start Host Isolation
    - Can set a custom unlock code, if a custom unlock code isn't provided it is randomly generated
- Stop Host Isolation

The analyzer comes in 5 flavors:
- AMPforEndpoints_**IsolationStart**: Start Host Isolation.
- AMPforEndpoints_**IsolationStop**: Stop Host Isolation.
- AMPforEndpoints_**MoveGUID**: Move Connector GUID to a new group.
- AMPforEndpoints_**SCDAdd**: Add SHA256 to a Simple Custom Detection List.
- AMPforEndpoints_**SCDRemove**: Remove SHA256 from a Simple Custom Detection List.

#### Requirements
You must have an AMP for Endpoints account and API Credentials with Read/Write API access.

To configure the analyzer, you must supply five parameters:
- `amp_cloud`: The FQDN AMP for Endpoints Cloud your account is in.
- `client_id`: AMP for Endpoints API Client ID.
- `api_key`: Password for the AMP for Endpoints API Client.
- `group_guid`: The Group GUID to move connectors into.
- `scd_guid`: The Simple Custom Detection List GUID to add and remove SHA256s.

### Crowdstrike Falcon
Submit observables from alerts and cases to the Crowdstrike Falcon Custom IOC API.

#### Requirements
To configure the responder, provide the URL of the platform as a value for the `falconapi_url` parameter, the api user as the `falconapi_user`parameter and the api key as the `falconapi_key` parameter.

### KnowBe4
This responder allows the integration between TheHive/Cortex and KnowBe4's User Events API. If a mail observable
 is tagged with a specified tag, corresponding to the responder's configuration, (e.g. phished), then the associated
  user will have a custom event added to their profile in KnowBe4.

#### Requirements
You must provide:

- An API key as a value for the `api_key` parameter to access the User Events API. API documentation to retrieve your
 key is located at [User Event API](https://developer.knowbe4.com/events/#tag/Introduction)
- The appropriate `base_url` parameter dependent on your geographic location. More information available at [User Events API](https://developer.knowbe4.com/events/#tag/Base-URL)
- The appropriate `hive_url` parameter so that TheHive case can be referenced in the KnowBe4 Users' Timeline
- The appropriate `event_type` parameter so that Cortex can create the [correct type of event](https://developer.knowbe4.com/events/#tag/Event-Types) in the Users' timeline.

### DomainTools Iris Malicious Tags

Add tag saying that the observable and case have a malicious tag based on iris tags short summary from the DomainTools Iris investigate analyzer.

#### Requirements

To configure the responder, provide a set of values for the `monitored_iris_tags` parameter.

### DomainTools Iris Risky DNS

Add tag saying that the observable and case contains a risky DNS based on risk score short summary from the DomainTools Iris investigate analyzer.

#### Requirements

To configure the responder, provide a value for the `high_risk_threshold` parameter.

### RT4-CreateTicket

RT4 ([Request Tracker](https://bestpractical.com/request-tracker) is a ticketing system. With this responder, an analyst can create a ticket in RT. CaseID is submitted to RT as a reference. 

This responder comes in only 1 flavor that lets you create a ticket from a Case in TheHive. 

#### requirements

This responder several configuration parameters. A detailed document can be found with the code, in the [Readme file](https://github.com/TheHive-Project/Cortex-Analyzers/tree/master/responders/RT4).

### Sendgrid

Send email by using [Sendrgrid](https://sendgrid.com/) services.

This responder comes in only 1 flavor.

#### Requirements

To run this responder, you need a valid account on [Sendgrid](https://sendgrid.com/) and define an email to use as the _from_ field as the `from` parameter, and a valid API key as the `api_key` parameter.

### Umbrella Blacklister
Add domain from observables in cases to Umbrella blacklist. 

#### Requirements
To configure the responder, provide the url of the service as a value for the `integration_url` parameter.

### VirusTotalDownloader

This responder comes in only 1 flavor that lets you download a sample of malware from VirusTotal by submitting a hash.

#### Requirements

This responder need a valid Premium API key from VirusTotal as the `virustotal_apikey` parameter in the configuration. 
To add the sample in Observables in TheHive, the responder also requires the URL of TheHive as the `thehive_url` paramenter and a valid API key as the `thehive_apikey` parameter.


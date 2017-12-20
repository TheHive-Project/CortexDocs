# Cortex Analyzer Requirements Guide
Analyzers are autonomous applications managed by and run through the Cortex core
engine. Analyzers allow analysts and security researchers to analyze 
observables and IOCs such as 
domain names, IP addresses, hashes, files, URLs at scale. While many 
analyzers are free to use, some require special access while others 
necessitate a valid service subscription or product license, even though the
analyzers themselves are released under an the [AGPL](https://github.com/TheHive-Project/Cortex-analyzers/blob/master/LICENSE)
(Affero General Public License).
  
This document outlines the information needed to:
- install the Cortex analyzers.
- update them when needed.
- configure them.

This documents also specifies whether the service that the analyzer is based 
on is free or requires special access or valid subscription or product license.

## Table of Contents
  * [Introduction](#introduction)
  * [Free Analyzers](#free-analyzers)
    * [Abuse\_Finder](#abuse_finder)
    * [CuckooSandbox](#cuckoosandbox)
    * [File\_Info](#file_info)
    * [FireHOLBlocklists](#fireholblocklists)
    * [Fortiguard](#fortiguard)
    * [GoogleSafeBrowsing](#googlesafebrowsing)
    * [Hippocampe](#hippocampe)
    * [HybridAnalysis](#hybridanalysis)
    * [MaxMind](#maxmind)
    * [MISP](#misp)
    * [Msg\_Parser](#msg_parser)
    * [OTXQuery](#otxquery)
    * [PhishTank](#phishtank)
    * [PhishingInitiative](#phishinginitiative)
    * [Virusshare](#virusshare)
    * [WOT](#wot)
    * [Yara](#yara)
    * [Yeti](#yeti)
  * [Analyzers Requiring Special Access](#analyzers-requiring-special-access)
    * [CERTatPassiveDNS](#certatpassivedns)
    * [CIRCLPassiveDNS](#circlpassivedns)
    * [CIRCLPassiveSSL](#circlpassivessl)
    * [Shodan](#shodan)
  * [Subscription and License\-based Analyzers](#subscription-and-license-based-analyzers)
    * [DNSDB](#dnsdb)
    * [DomainTools](#domaintools)
    * [EmergingThreats](#emergingthreats)
    * [JoeSandbox](#joesandbox)
    * [PassiveTotal](#passivetotal)
    * [Nessus](#nessus)
    * [VirusTotal](#virustotal)
    * [VMRay](#vmray)

## Introduction
All analyzer configuration settings must be made in the global Cortex 
configuration 
file (`/etc/cortex/application.conf` by default), in the `config` section.

By default, all analyzers are enabled. If you want to disable some of them, 
add a 
`disabled` list to `/etc/cortex.application.conf` with the full name of the 
analyzer (or flavor), including its version. For example, if you'd like to 
disable 
the 
Abuse_Finder (version 2.0 as of this writing) analyzer and the DNSDB DomainName 
flavor (version 2.0 as of this writing) of the DNSDB analyzer, add the 
following line to the `analyzer` section:
```text
analyzer {
  # Absolute path where you have pulled the Cortex-Analyzers repository.
  path = "/opt/Cortex-Analyzers/analyzers"

  disabled = ["Abuse_Finder_2_0", "DNSDB_DomainName_2_0"]

  # Sane defaults. Do not change unless you know what you are doing.
  fork-join-executor {
  [...]
  }
  [...]
}
```

If you have troubles finding out the exact name of the analyzer or flavor you'd 
like to disable, you can use a command like the following to list the full 
names of all the analyzers/flavors:

```commandline
$ grep "Register analyzer" /var/log/cortex/application.log \
| sed -e 's/.*(\([0-9a-zA-Z_]\+\))$/\1/' | sort -u
```

The output should look like the following:
```commandline
Abuse_Finder_2_0
CERTatPassiveDNS_2_0
CIRCLPassiveDNS_2_0
CIRCLPassiveSSL_2_0
CuckooSandbox_File_Analysis_Inet_1_0
CuckooSandbox_Url_Analysis_1_0
DNSDB_DomainName_2_0
DNSDB_IPHistory_2_0
DNSDB_NameHistory_2_0
DomainTools_ReverseIP_2_0
DomainTools_ReverseNameServer_2_0
DomainTools_ReverseWhois_2_0
DomainTools_WhoisHistory_2_0
[...]
```
## Free Analyzers

### Abuse_Finder
Use CERT-SG's [Abuse Finder](https://github.com/certsocietegenerale/abuse_finder)
to find abuse contacts associated with domain names, URLs, IPs and email addresses.

The analyzer comes in only one flavor.

No configuration is required. The analyzer has no entry in the `config` section. 
It can be used out of the box.

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

To configure the analyzer you need to supply the URL of your local instance 
as a value of the `url` parameter.

#### Example:
```text
CuckooSandbox {
    url = "http://my.cuckoo.sandbox"
}
```

### File_Info
Parse files in several formats such as OLE and OpenXML to detect VBA macros, 
extract their source code, generate useful information on PE, PDF files and much more.

The analyzer comes in only one flavor.

No configuration is required. The analyzer has no entry in the `config` section. It can be used out 
of the box.

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

#### Example
```text
    FireHOLBlocklists {
      blocklistpath = "/opt/firehol/blocklists"
      ignoreolderthandays="7" # ignore all lists not updated in the last 7d
    }
```

### Fortiguard
Check the [Fortiguard](https://fortiguard.com/webfilter) category of a URL or
 a domain.

The analyzer comes in only one flavor called *Fortiguard_URLCategory*.

No configuration is required. The analyzer has no entry in the `config` section. It can be used out 
of the box.

### GoogleSafeBrowsing
Check URLs against [Google Safebrowsing](https://www.google.com/transparencyreport/safebrowsing/).

The analyzer comes in only one flavor. 

#### Requirements
You need to [obtain an API key](https://developers.google.com/safe-browsing/)
 from Google.

Provide your API key as a value of the `key` parameter.

#### Example
```text
    GoogleSafebrowsing {
      key = "MYKEY"
    }
```

### Hippocampe
Query threat feeds through [Hippocampe](https://github.com/CERT-BDF/Hippocampe), 
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

#### Example
```text
    Hippocampe {
      url = "http://my.hippocampe.instance"
    }
```

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

#### Example
```text
    HybridAnalysis {
      secret = "mysecret"
      key = "myAPIkey"
    }
```

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

No configuration is required. The analyzer has no entry in the `config` section. It can be used out 
of the box.

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

#### Example
The example below shows the configuration of the MISP analyzer which will 
search two MISP instances called MY-OWN-MISP and REMOTE-MISP. Note that the 
first one is accessed through HTTP while the second has HTTPS enabled.

```text
    MISP {
      url=["http://my.own.misp", "https://remote-misp.peercert.org"]
      key=["my-own-misp-account-authkey", "remote-misp-account-authkey"]
      certpath=[ false, "/etc/ssl/certs" ]
      name=["MY-OWN-MISP","REMOTE-MISP"]
    }
```

### Msg_Parser
Parse Outlook message files automatically and show the key information it 
contains such as headers, attachments etc. Please note that the analyzer 
doesn't extract attachments.

The analyzer comes in only one flavor.

No configuration is required. The analyzer has no entry in the `config` section. It can be used out 
of the box.

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

#### Example
```text
    OTXQuery {
      key="MYUBERSEKRETOTXQUERYKEY"
    }
```

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

#### Example
```text
    PhishTank {
      key="MYPHISHTANKAPIKEYGOESHERE"
    }
```

### PhishingInitiative
Query [Phishing Initiative](https://phishing-initiative.fr/contrib/) to 
assess whether a URL has been flagged as a phishing site.

This analyzer comes in only one flavor called *PhishingInitiative_Lookup*.

#### Requirements
You need to sign up for a [Phishing Initiative](https://phishing-initiative.fr/register)
 account or use an existing one.

Log in to your Phishing Initiative account, click on the icon representing 
your account details then on *API*. Retrieve the API key value and supply 
it as the value to the `key` configuration parameter.

#### Example
```text
    PhishingInitiative {
      key="MYPHISHINGINITIATIVEAPIKEYGOESHERE"
    }
```

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

#### Example
```text
    Virusshare {
      path = "/path/to/virusshare/lists"
    }
```

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

#### Example
```text
    WOT {
      key="myWOTAPIkey"
    }
```

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

#### Example
In the example shown below, the first two locations are directories. As such,
 the analyzer will expect to find an *index.yar* or *index.yas* file in these
  directories:

```text
Yara {
    rules=["/path/dirA", "/path/dirB", "/path/my/rules.yar"]
}
```

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

Provide the URL of your YETI instance as a value for the `url` parameter.

#### Example
```text
Yeti {
    url = "http://my.yeti.instance:5000"
}
```

## Analyzers Requiring Special Access
### CERTatPassiveDNS
Check CERT.at Passive DNS Service for a given domain.

This analyzer comes in only one flavor.

#### Requirements
Access to the CERT.at service is allowed to trusted partners only. If you 
think you qualify, please contact [CERT.at](http://www.cert.at/index_en.html).

No configuration is required. The analyzer has no entry in the `config` section. It can be used out 
of the box if CERT.at positively answers your access request.

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

#### Example
```text
    CIRCLPassiveDNS {
      user= "myusername"
      password= "mypassword"
    }
```

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

supply your username as the value for the `user` parameter and your password 
as the value for the `password` parameter.

#### Example
```text
    CIRCLPassiveSSL {
      user= "myusername"
      password= "mypassword"
    }
```

### Shodan
Retrieve key [Shodan](https://www.shodan.io/) information on domains and IP addresses. 

This analyzer comes in two flavors:
- Shodan_**Host**: get Shodan information on a host.
- Shodan_**Search**: get Shodan information on a domain.

#### Requirements
You need to create a Shodan account and retrieve the associated API Key. For 
best results, it is advised to get a [Membership](https://enterprise.shodan.io/product-comparison)
level account, otherwise a free one can be used. 

Supply the API key as the value for the `key` parameter.

#### Example
```text
    Shodan {
      key = "myawesomeapikey"
    }
```

## Subscription and License-based Analyzers
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
Farsight 
Security.

Provide your API key as a value to the `key` parameter. 

#### Example
```text
    DNSDB {
      server="https://api.dnsdb.info"
      key="MYDNSDBAPIKEY"
    }
```
### DomainTools
Look up domain names, IP addresses, WHOIS records, etc. using the popular 
[DomainTools](http://domaintools.com/) service API.

The analyzer comes in 5 flavors:
- DomainTools_**ReverseIP**: get a list of domain names sharing the same IP 
address.
- DomainTools_**ReverseNameServer**: get a list of domain names that share 
the same primary or secondary name server.
- DomainTools_**ReverseWhois**: get a list of domain names which share the 
same registrant information.
- DomainTools_**WhoisHistory**: get a list of historical Whois records 
associated with a domain name.
- DomainTools_**WhoisLookup**: get the ownership record for a domain with 
basic registration details.
- DomainTools_**WhoisLookup_IP**: get the ownership record for an IP address 
with basic registration details.

#### Requirements
You need a [valid DomainTools API integration subscription](https://www.domaintools.com/products/api-integration/)
to use the analyzer.

Provide your username as a value for the `username` parameter and API key as 
a value for the `key` parameter.

#### Example
```text
    DomainTools {
      username="mightyhunter"
      key="SOMELONGWEIRDSTRING"
    }
```

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

#### Example
```text
    EmergingThreats {
      key = "MYETINTELKEYGOESHERE"
    }
```   

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

#### Example
```text
    JoeSandbox {
      url = "https://my.cool.joe.sandbox"
      key = "myJoeKey"
    }
``` 

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

#### Requirements
You need a PassiveTotal account to obtain the API key which is required to 
use the analyzer. If you sign up for a [Community Edition Account](https://community.riskiq.com/registration),
you'll have a very limited number of queries. You can purchase a 
[PassiveTotal subscription](https://www.riskiq.com/contact/) for a higher 
number of queries per day.
 
Provide your account's username as the value of the `username` parameter and 
the associated API key as value for the `key` parameter.

#### Example
```text
    PassiveTotal {
      username="digitalhunter"
      key="mypassivetotalAPIkey"
```

### Nessus
Use [Nessus Professional](https://www.tenable.com/products/nessus-vulnerability-scanner),
a popular vulnerability scanner to scan an IP address or a FQDN.

The analyzer comes in only one flavor.

#### Requirements
You must have a locally deployed instance of Nessus Professional to use the 
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

#### Example
```text
    Nessus {
      url ="https://my.nessus.scanner"
      login="bastardoperator"
      password="secretpassword"
      policy="MyScanPolicy"
      ca_bundle="/etc/ssl/certs"
      allowed_networks=[ '10.0.0.0/8', '192.168.1.0/24' ]
    }
```

### VirusTotal
Look up files, URLs and hashes in [VirusTotal](https://www.virustotal.com/).

The analyzer comes in two flavors:
- VirusTotal_**GetReport**: get the latest VirusTotal report for a file, 
hash, domain or an IP address.
- VirusTotal_**Scan**: scan a file or URL.

#### Requirements
You need a [VirusTotal](https://www.virustotal.com/#/join-us) community 
account or a [Private API](https://support.virustotal.com/hc/en-us/requests/new)
subscription, a premium service.

Please note that a community account is highly limited in the number of API 
queries it can make. If you can afford them, subscribe to the premium services.

Provide the API key associated with your account as a value to the `key` 
parameter.

#### Example
```text
    VirusTotal {
      key="myVTkey"
    }
```

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

#### Example
```text
    VMRay {
      url = "https://my.vmray.analyzer.platform"
      key = "myAssociatedApiKey"
      certpath = "/etc/ssl/certs"
 }
``` 

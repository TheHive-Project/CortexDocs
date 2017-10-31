# DRAFT -- WIP
Please do not make corrections or submit PRs at this time as the document is 
undergoing changes.

# Cortex Analyzer Requirements
This document outlines the different information that needs to be provided for 
each analyzer, such as API keys, usernames, instances, etc. It will also 
specify whether the service is paid, free, or requires special access. This 
will assist users in deciding what analyzers they want to enable and what 
information they need to gather in order to enable them.

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
    * [JoeSandbox](#joesandbox)
    * [MISP](#misp)
    * [Msg\_Parser](#msg_parser)
    * [OTXQuery](#otxquery)
    * [PassiveTotal](#passivetotal)
    * [PhishTank](#phishtank)
    * [Phishing Intiative](#phishing-intiative)
    * [VirusTotal](#virustotal)
    * [Virusshare](#virusshare)
    * [WOT](#wot)
    * [Yara](#yara)
    * [YETI](#yeti)
  * [Paid](#paid)
    * [DNSDB](#dnsdb)
    * [DomainTools](#domaintools)
    * [MaxMind](#maxmind)
    * [Nessus](#nessus)
    * [VMRay](#vmray)
  * [Special Access](#special-access)
    * [CERTatPassiveDNS](#certatpassivedns)
    * [CIRCLPassiveDNS](#circlpassivedns)
    * [CIRCLPassiveSSL](#circlpassivessl)

## Introduction
All configuration settings must be made in the global Cortex 
configuration 
file (`/etc/cortex/application.conf` by default), in the `config` section.

By default, all analyzers are enabled. If you want to disable some of them, 
add a 
`disabled` list to `/etc/cortex.application.conf` with the full name of the 
analyzer, including its version. For example, if you'd like to disable the 
Abuse Finder (version 2.0 as of this writing) and DNSDB DomainName (same 
version as of this writing), add the following line in the `analyzer` section:
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

If you have troubles finding out the exact name of the analyzer you'd like to
 disable, you can use a command like the following to list the full names of 
 all the analyzers:

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
#### Description
Use CERT-SG's [Abuse Finder](https://github.com/certsocietegenerale/abuse_finder) to fin abuse contacts associated with domain 
names, 
URLs, IPs and email addresses.

The analyzer comes in only one flavor.

#### Configuration
None. The analyzer has no entry in the `config` section. It can be used out 
of the box.

### CuckooSandbox
#### Description
Analyze URLs and files using [Cuckoo Sandbox](https://cuckoosandbox.org/).

The analyzer comes in two flavors:

- CuckooSandbox_**File_Analysis_Inet**: submit files for analysis to a Cuckoo 
Sandbox instance with Internet access.
- CuckooSandbox_**Url_Analysis**: submit URLs for analysis to a Cuckoo Sandbox 
instance.

#### Configuration
##### Requirements
The CuckooSandbox analyzer requires you to have a local instance
 of Cuckoo Sandbox deployed.
It is an open source tool that is free for use but needs to be manually 
deployed in your environment. Please go to 
[https://cuckoosandbox.org/](https://cuckoosandbox.org/) 
for more information on setting it up.

##### Parameters
To configure the analyzer you need to supply the URL of your local instance 
using the `url` keyword.

Example:
```text
CuckooSandbox {
    url = "http://my.cuckoo.sandbox"
}
```

### File_Info
#### Description
Parse files in several formats such as OLE and OpenXML to detect VBA macros, 
extract their source code, generate useful information on PE, PDF files and much more.

The analyzer comes in only one flavor.

#### Configuration
None. The analyzer has no entry in the `config` section. It can be used out 
of the box.


### FireHOLBlocklists 
#### Description
Check IP addresses against the [FireHOL blocklists](https://firehol.org/).

The analyzer comes in only one flavor.

#### Configuration
##### Requirements
This analyzer needs you to download the FireHOL block lists first to a 
directory. Use `git` for that  purpose:
```commandline
$ mkdir /path/to/firehol
$ cd /path/to/firehol
$ git clone https://github.com/firehol/blocklist-ipsets 
```

We advise you to keep the lists fresh  by  adding  a  cron  entry  to 
regularly download them for example (using `git pull`).

#### Parameters
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
#### Description
Check the [Fortiguard](https://fortiguard.com/webfilter) category of a URL or
 a domain.

The analyzer comes in only one flavor called *Fortiguard_URLCategory*.

#### Configuration
None. The analyzer has no entry in the `config` section. It can be used out 
of the box.

### GoogleSafeBrowsing
#### Description
Check URLs against [Google Safebrowsing](https://www.google.com/transparencyreport/safebrowsing/).

The analyzer comes in only one flavor. 

#### Configuration
##### Requirements
You need to [obtain an API key](https://developers.google.com/safe-browsing/)
 from Google.

##### Parameters
Provide your API key as a value of the `key` parameter.

#### Example
```text
    GoogleSafebrowsing {
      key = "MYKEY"
    }
```

### Hippocampe
#### Description
Query threat feeds through [Hippocampe](https://github.com/CERT-BDF/Hippocampe), 
a FOSS tool from TheHive Project that centralizes feeds and allows you to 
associate a confidence level to each one of them (that can be changed over time)
 and get a score indicating the data quality.
 
The analyzer comes in two flavors:
- HippoMore: get the Hippocampe detailed report for an IP address, a domain or
 a URL.
- Hipposcore: get the Hippocampe Score report associated with an IP address, a
 domain or a URL.

#### Configuration
##### Requirements
The Hippocampe analyzer requires you to have a local instance of Hippocampe 
deployed/configured. It is an open source tool that is free for use but needs
 to be manually deployed in your environment. Please go to [https://github.com/CERT-BDF/Hippocampe](https://github.com/CERT-BDF/Hippocampe)
 for more in information on setting it up.

##### Parameters
To configure the analyzer you need to supply the URL of your local instance 
using the `url` keyword.

##### Example
```text
Hippocampe {
    url = "http://my.hippocampe.instance"
}
```

### MaxMind
#### Description
Geolocate an IP Address via [MaxMind](https://www.maxmind.com/en/home) 
GeoLite2 **free** City and Country databases.

Cortex does not refresh those databases automatically. It is up to you to 
create a cron job to refresh them at the frequency you want. The files to 
update are:

- `MaxMind/GeoLite2-City.mmdb`
- `MaxMind/GeoLite2-Country.mmdb`

You can fetch up-to-date versions from [https://dev.maxmind.com/geoip/geoip2/geolite2/](https://dev.maxmind.com/geoip/geoip2/geolite2/).

The analyzer comes in only one flavor.

#### Configuration
None. The analyzer has no entry in the `config` section. It can be used out 
of the box.

### MISP
#### Description
Query multiple MISP (Malware Information Sharing Platform )instances for 
events containing an observable.

[MISP](http://www.misp-project.org/) is an open source threat sharing 
platform and considered 
the *de facto* standard in the field. You'd benefit greatly from using it in 
conjunction to Cortex and TheHive as these 3 FOSS products make an 
interesting Threat Intelligence, Incident Response and Digital Forensics 
ecosystem.

The analyzer comes in only one flavor. 

#### Configuration
##### Requirements
The MISP analyzer requires you to have access to one or several [MISP](http://www.misp-project.org/) 
 instances. You can also deploy your own instance.

##### Parameters
Four parameters are required:
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

- Use `""` if you don't want to validate the instance's X.509 certificate or 
if the instance use old plain HTTP.
- Use `"/etc/ssl/certs"` or another file to validate the instance's X.509 
certificate.

Last but not least, give each instance a name and add it in the order you 
specified URLs and keys above to the `name` dict.

##### Example
The example below shows the configuration of the MISP analyzer which will 
search two MISP instances called MY-OWN-MISP and REMOTE-MISP. Note that the 
first one is accessed through HTTP while the second has HTTPS enabled.

```text
    MISP {
      url=["http://my.own.misp", "https://remote-misp.peercert.org"]
      key=["my-own-misp-account-authkey", "remote-misp-account-authkey"]
      certpath=["","/etc/ssl/certs"]
      name=["MY-OWN-MISP","REMOTE-MISP"]
```

### Msg_Parser
#### Description
Parse Outlook message files automatically and show the key information it 
contains such as headers, attachments etc. Please note that the analyzer 
doesn't extract attachments.

The analyzer comes in only one flavor.

#### Configuration
None. The analyzer has no entry in the `config` section. It can be used out 
of the box.

### OTXQuery
#### Description
Query AlienVault's [Open Threat Exchange](https://otx.alienvault.com/) for IPs, 
domains, URLs, or file hashes.

The analyzer comes in only one flavor.

#### Configuration
##### Requirements
You need to sign up for an [OTX](https://otx.alienvault.com/) account or use 
an existing one.

##### Parameters
Log in to your OTX account, click on your username on the top 
navigation bar then on *Settings* and retrieve your OTX key and use it as the 
value of the `key` parameter.

##### Example
```text
    OTXQuery {
      key="MYUBERSEKRETOTXQUERYKEY"
    }
```

### PhishTank
#### Description
Query [PhishTank](https://www.phishtank.com/) to assess whether a URL has 
been flagged a phishing site.

The analyzer comes in only one flavor called *PhishTank_CheckURL*.

#### Configuration
##### Requirements
You need to sign up for a [PhishTank](https://www.phishtank.com/register.php)
 account or use an existing one.

##### Parameters
Log in to your PhishTank account, click on the *Developers* tab then on 
*Manage Applications*, register an application by giving it a name and 
entering a CAPTCHA code. You'll obtain an API key that you'll need to supply 
as the value to the `key` configuration parameter for this analyzer to work.

##### Example
```text
    PhishTank {
      key="MYPHISHTANKAPIKEYGOESHERE"
    }
```

### PhishingInitiative
#### Description
Query [Phishing Initiative](https://phishing-initiative.fr/contrib/) to 
assess whether a URL has been flagged a phishing site.

This analyzer comes in only one flavor called *PhishingInitiative_Lookup*.

#### Configuration
##### Requirements
You need to sign up for a [Phishing Initiative](https://phishing-initiative.fr/register)
 account or use an existing one.

##### Parameters
Log in to your Phishing Initiative account, click on the icon representing 
your account details then on *API*. Retrieve the API key value and supply 
it as the value to the `key` configuration parameter.

##### Example
```text
    PhishingInitiative {
      key="MYPHISHINGINITIATIVEAPIKEYGOESHERE"
    }
```

**PROGRESS_MARK**

### Virusshare
**Configuration Parameters**: Path to Virusshare hash lists

Virusshare hash lists can be downloaded using the `download_hashes.py` program in the Virusshare analyzer directory.

### WOT
**Configuration Parameters**: API Key

An account with Web of Trust is required to get an API key. You can sign up for an account [here](https://www.mywot.com/en/signup?destination=profile/api).

### Yara
**Configuration Parameters**: Path to Yara rules

You have to have Yara rules downloaded in order to use this analyzer. 

### Yeti
**Configuration Parameters**: Url 

The YETI analyzer requires you to have a local instance of YETI deployed/configured. It is an open-source tool that is free for use but needs to be manually deployed in your environment. More information on setting up YETI can be found [here](https://yeti-platform.github.io/).

## Paid  

- DNSDB
- DomainTools
- JoeSandbox
- Nessus
- PassiveTotal
- VirusTotal
- VMRay

### JoeSandbox
**Configuration Parameters**: _TBD_

JoeSandbox has both a free and a paid version. 

### PassiveTotal
**Configuration Parameters**: Username, API Key

An account with PassiveTotal is required to get an API key. You can sign up for an account [here](https://community.riskiq.com/registration).
### DNSDB
**Configuration Parameters**: Server name, API key

This is a paid service that can be purchased from [here](https://www.farsightsecurity.com/order-services/). 

### DomainTools
**Configuration Parameters**: Username, API key

This is a paid service that can be purchased from [here](https://www.domaintools.com/products/api-integration/). There is also a free API that can be used (needs to be set to free in analyzer config) and does not require a username/API key. 


### Nessus
**Configuration Parameters**: Url, login, password, policy

This is a paid service that can be purchased from [here](https://www.tenable.com/products/nessus-vulnerability-scanner). 

### VMRay
**Configuration Parameters**: Url, API key

This is a paid service that can be purchased from [here](https://www.vmray.com/). 

### VirusTotal
**Configuration Parameters**: API Key

An account with VirusTotal is required to get an API key. You can sign up for an account [here](https://www.virustotal.com/en/).


## Special Access

### CERTatPassiveDNS
**Configuration Parameters**: None

From [TheHive blog](https://blog.thehive-project.org/2017/07/05/all-fresh-cortexutils-new-cortex-analyzers/):
```
Access to the CERT.at service is allowed to trusted partners only.  
If you think you qualify, please contact CERT.at.  You do not need to add specific information 
into the Cortex configuration file to benefit from this analyzer as it calls the whois system 
command to perform the pDNS requests.
```

### CIRCLPassiveDNS
**Configuration Parameters**: Username, Password

From [CIRCL](https://www.circl.lu/services/passive-dns/)
```
Access to CIRCL Passive DNS is only allowed to trusted partners in Luxembourg and abroad. 
Contact us if you would like access. Include your affiliation and the foreseen use of the 
Passive DNS data.
```

### CIRCLPassiveSSL
**Configuration Parameters**: Username, Password

From [CIRCL](https://www.circl.lu/services/passive-ssl/)
```
Access to CIRCL Passive SSL is allowed to partners including security researchers or 
incident analysts worldwide. Contact us if you would like to obtain access.
```


**NOTE**: If there are any errors on this page or changes you would like to see, please submit a pull request or email megansroddie[at]gmail.com

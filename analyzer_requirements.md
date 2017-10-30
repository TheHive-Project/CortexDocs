# WARNING: DRAFT

# Cortex Analyzer Requirements
This document outlines the different information that needs to be provided for each analyzer, such as API keys, usernames, instances, etc. It will also specify whether the service is paid, free, or requires special access. This will assist users in deciding what analyzers they want to enable and what information they need to gather in order to enable them.

All configuration settings must be made in the global Cortex configuration 
file (`/etc/cortex/application.conf` by default).

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
## Free

### Abuse_Finder
**Configuration Parameters**: None

### CuckooSandbox
**Configuration Parameters**: Url

The CuckooSandbox analyzer requires you to have a local instance of Cuckoo Sandbox deployed. It is an open-source tool that is free for use but needs to be manually deployed in your environment. More information on setting up Cuckoo Sandbox can be found [here](https://cuckoosandbox.org/).

### File_Info
**Configuration Parameters**: None

### FireHOL Blocklist 
**Configuration Parameters**: Path to FireHOL blocklists

FireHOL blocklists can be downloaded from [here](https://iplists.firehol.org)

### Fortiguard
**Configuration Parameters**: None

### Google SafeBrowsing
**Configuration Parameters**: API Key

An account with Google is required to get an API key.

### Hippocampe
**Configuration Parameters**: Url

The Hippocampe analyzer requires you to have a local instance of Hippocampe deployed/configured. It is an open-source tool that is free for use but needs to be manually deployed in your environment. More information on setting up Hippocampe can be found [here](https://github.com/CERT-BDF/Hippocampe).

### JoeSandbox
**Configuration Parameters**: _TBD_

JoeSandbox has both a free and a paid version. 

### MISP
**Configuration Parameters**: Url, API key

The MISP analyzer requires you to have a local instance of MISP deployed/configured. It is an open-source tool that is free for use but needs to be manually deployed in your environment. More information on setting up MISP can be found [here](http://www.misp-project.org/).

### Msg_Parser
**Configuration Parameters**: None

### OTXQuery
**Configuration Parameters**: API Key

An account with AlienVault OTX is required to get an API key. You can sign up for an account [here](https://otx.alienvault.com).

### PassiveTotal
**Configuration Parameters**: Username, API Key

An account with PassiveTotal is required to get an API key. You can sign up for an account [here](https://community.riskiq.com/registration).

### PhishTank
**Configuration Parameters**: API Key

An account with PhishTank is required to get an API key. You can sign up for an account [here](https://www.phishtank.com/index.php).

### Phishing Intiative
**Configuration Parameters**: API Key

An account with Phishing Intiative is required to get an API key. You can sign up for an account [here](https://phishing-initiative.fr/register).

### VirusTotal
**Configuration Parameters**: API Key

An account with VirusTotal is required to get an API key. You can sign up for an account [here](https://www.virustotal.com/en/).

### Virusshare
**Configuration Parameters**: Path to Virusshare hash lists

Virusshare hash lists can be downloaded using the `download_hashes.py` program in the Virusshare analyzer directory.

### WOT
**Configuration Parameters**: API Key

An account with Web of Trust is required to get an API key. You can sign up for an account [here](https://www.mywot.com/en/signup?destination=profile/api).

### Yara
**Configuration Parameters**: Path to Yara rules

You have to have Yara rules downloaded in order to use this analyzer. 

### YETI
**Configuration Parameters**: Url 

The YETI analyzer requires you to have a local instance of YETI deployed/configured. It is an open-source tool that is free for use but needs to be manually deployed in your environment. More information on setting up YETI can be found [here](https://yeti-platform.github.io/).

## Paid  
### DNSDB
**Configuration Parameters**: Server name, API key

This is a paid service that can be purchased from [here](https://www.farsightsecurity.com/order-services/). 

### DomainTools
**Configuration Parameters**: Username, API key

This is a paid service that can be purchased from [here](https://www.domaintools.com/products/api-integration/). There is also a free API that can be used (needs to be set to free in analyzer config) and does not require a username/API key. 

### MaxMind
**Configuration Parameters**: _TBD_

This is a paid service that can be purchased from [here](https://www.maxmind.com/en/home). 

### Nessus
**Configuration Parameters**: Url, login, password, policy

This is a paid service that can be purchased from [here](https://www.tenable.com/products/nessus-vulnerability-scanner). 

### VMRay
**Configuration Parameters**: Url, API key

This is a paid service that can be purchased from [here](https://www.vmray.com/). 



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

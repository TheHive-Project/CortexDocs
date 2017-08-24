# Cortex Analyzer Requirements
This document outlines the different information that needs to be provided for each analyzer, such as API keys, usernames, instances, etc. It will also specify whether the service is paid, free, or requires special access. This will assist users in deciding what analyzers they want to enable and what information they need to gather in order to enable them.

## Free

### Abuse_Finder
**Configuration Paramenters**: None

### CuckooSandbox
**Configuration Paramenters**: <fill in>

The CuckooSandbox analyzer requires you to have a local instance of Cuckoo Sandbox deployed. It is an open-source tool that is free for use but needs to be manually deployed in your environment. More information on setting up Cuckoo Sandbox can be found [here](https://cuckoosandbox.org/).

## Paid  

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
**Configuration Paramenters**: 

### CIRCLPassiveSSL
**Configuration Paramenters**: 

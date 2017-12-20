# MISP Integration
Starting from version 1.1.1, Cortex can be integrated in two ways with [MISP](http://www.misp-project.org/):

- Cortex can [invoke MISP modules](#invoke-misp-modules-within-cortex)
- MISP can [invoke Cortex analyzers](#invoke-cortex-analyzers-within-misp)

##### Important Note
To use Cortex with MISP, you need MISP version 2.4.73 or better. Earlier versions cannot invoke Cortex analyzers. 

## Invoke MISP Modules Within Cortex
Besides its [regular analyzers](https://github.com/thehive-project/cortex/#analyzers), Cortex 1.1.1 and above can analyze observables using
[MISP expansion modules](https://github.com/MISP/misp-modules#expansion-modules).

Obviously, there are some overlap between Cortex native analyzers and MISP expansion modules. For example, you could query the CIRCL's Passive DNS service using a [native Cortex analyzer](https://github.com/TheHive-Project/Cortex-Analyzers/tree/master/analyzers/CIRCLPassiveDNS) or a [MISP expansion module](https://github.com/MISP/misp-modules/blob/master/misp_modules/modules/expansion/circl_passivedns.py). When there's overlap, we highly recommend you rely on the Cortex analyzer. That way, we will be able to better help you in case you encounter issues or need help to make it work.

In order to invoke MISP expansion modules within Cortex, they need to be installed on the same host that Cortex runs on. To install the modules, follow the [MISP documentation](https://github.com/MISP/misp-modules#how-to-install-and-start-misp-modules) pertaining to this topic. 

A sample set of commands to perform the operation is provided below. Nonetheless we advise you to read the above-mentioned documentation before proceeding with the installation.

```
sudo apt-get install python3-dev python3-pip libpq5 libjpeg-dev
cd /usr/local/src/
sudo git clone https://github.com/MISP/misp-modules.git
cd misp-modules
sudo pip3 install -I -r REQUIREMENTS
sudo pip3 install -I .
```

Once you have installed the MISP modules, you must enable them in Cortex by adding the following lines in Cortex `application.conf`:

```
misp.modules.enabled = true
```

Most MISP modules must be configured prior to their use. Their settings must be placed inside the `misp.modules` > `config` key in the Cortex `application.conf` file. 

**Warning**: MISP expansion modules will not load if they are not configured properly.

```
misp.modules {
  enabled = true

  config {
  
    shodan {
      apikey = ""
    }
    
    dns {
      nameserver = "127.0.0.1"
    }
    
  }
}
```

In order to run the modules, Cortex relies on a Python wrapper which is located in the `contrib` folder. Cortex should be able to locate it automatically but you can force its location in configuration under settings:

```
misp.modules.loader = /path/to/misp-modules-loader.py"
```

## Invoke Cortex Analyzers within MISP
Starting from version 2.4.73, a MISP instance can invoke Cortex analyzers. To do so, connect to the MISP Web UI with sufficient privileges, then go to `Administration` > `Server settings` > `Plugin settings`. Edit the Cortex section as shown below:

 - set `Plugin.Cortex_services_enable` to `true`
 - set `Plugin.Cortex_services_url` to `http://<ip_address>` (replace `<ip_address>` with the IP address of Cortex)
 - set `Plugin.Plugin.Cortex_services_port` to `<port>` (replace `<port>` with the port on which Cortex is listening: 9000 by default)

Once this operation is completed, the Cortex analyzer list should appear in MISP's Cortex section. The analyzers must be enabled to make them available to the instance's users.

 

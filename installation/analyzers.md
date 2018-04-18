# Cortex Analyzers

Analyzers are autonomous applications managed by and run through the Cortex core engine. Analyzers have their
[own dedicated GitHub repository](https://github.com/TheHive-Project/Cortex-Analyzers).
They are included in the Cortex binary, RPM and DEB packages but you have to
get them from the repository if you need to update them after installing one
of those packages (which is necessary when new analyzers are released or new
versions of existing ones are made available) ordecide to build Cortex from
sources.

## Pre-requisites
Currently, all provided analyzers are written in Python. They don't require any build phase but their dependencies have
to be installed. Before proceeding, you'll need to install the system package dependencies that are required by some of
them:

```
apt-get install python-pip python2.7-dev ssdeep libfuzzy-dev libfuzzy2 libimage-exiftool-perl libmagic1 build-essential libssl-dev
```

Each analyzer comes with its own, pip compatible `requirements.txt` file. You can install all requirements with the
following commands:

```
cd analyzers
sort -u */requirements.txt > /tmp/requirements.txt && \
sudo -H pip install -r /tmp/requirements.txt && rm /tmp/requirements.txt
```

## From repository
If you want to use up-to-date analyzers, you can clone the GitHub repository:

```
git clone https://github.com/CERT-BDF/Cortex-Analyzers
```

Next, you'll need to tell Cortex where to find the analyzers. Analyzers may be in different directories as shown in this dummy example of the Cortex configuration file (`application.conf`):

```
analyzer {
  # Directory that holds analyzers
  path = [
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
```
## Configuration
All analyzers must be configured using the Web UI.

Please read the [Quick Start Guide](../admin/quick-start.md) to create at least one organization then let a user with the `orgAdmin` role configure and enable analyzers for that organization.

Some analyzers can be used out of the box, without any configuration, while others may require various parameters. Please check the [Analyzer Requirements Guide](../analyzer_requirements.md) for further details.

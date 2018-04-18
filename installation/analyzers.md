# Cortex Analyzers

Analyzers are autonomous applications managed by and run through the Cortex core engine. Analyzers have their
[own dedicated GitHub repository](https://github.com/TheHive-Project/Cortex-Analyzers).
They are included in the Cortex binary, RPM and DEB packages but you have to
get them from the repository if you need to update them after installing one
of those packages (which is necessary when new analyzers are released or new
versions of existing ones are made available) or if you decide to build Cortex from
sources.

## Installation
Currently, all provided analyzers are written in Python 2 or 3. They don't require any build phase but their dependencies have
to be installed. Before proceeding, you'll need to install the system package dependencies that are required by some of
them:

```
sudo apt-get install -y --no-install-recommends python-pip python2.7-dev python3-pip python3-dev ssdeep libfuzzy-dev libfuzzy2 libimage-exiftool-perl libmagic1 build-essential git libssl-dev
```

You may need to install Python's `setuptools` and update pip/pip3:
```
sudo pip install -U pip setuptools && sudo pip3 install -U pip setuptools
```

Once finished, clone the Cortex-analyzers repository in the directory of your choosing:
```
git clone https://github.com/TheHive-Project/Cortex-Analyzers

```

Each analyzer comes with its own, pip compatible `requirements.txt` file. You can install all requirements with the
following commands:

```
for I in Cortex-Analyzers/analyzers/*/requirements.txt; do sudo -H pip2 install -r $I; done && \
for I in Cortex-Analyzers/analyzers/*/requirements.txt; do sudo -H pip3 install -r $I || true; done
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

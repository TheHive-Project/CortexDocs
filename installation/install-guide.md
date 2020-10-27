# Installation Guide

---
⚠️   _**Please read carrefully this documentation. Depending on you make a fresh installation or update an existing version, repository or packages names may vary.**_

Current supported versions of Cortex are:
- Version 3.1.0 and later that supports **only** Elasticsearch 7.x. 

Instruction to  install Cortex supporting Elasticsearch 6.x (EoL in Nov. 2020) are still detailled in this documentation. 

---

Before installing Cortex, you need to choose the installation option which suits your environment as described below, install the analyzers then proceed to the configuration using the [Quick Start Guide](../admin/quick-start.md). For more advanced configuration options, please refer to the [Administration Guide](../admin/admin-guide.md).


## Table of Contents
   * [Table of Contents](#table-of-contents)
  * [Installation Options](#installation-options)
    * [RPM](#rpm)
    * [DEB](#deb)
    * [Docker](#docker)
    * [Binary](#binary)
    * [Build it Yourself](#build-it-yourself)
  * [Analyzers and Responders](#analyzers-and-responders)
    * [Installation](#installation)
    * [Configuration](#configuration)
    * [Updating](#updating)
    * [Additional Analyzers](#additional-analyzers)
  * [Elasticsearch Installation](#elasticsearch-installation)
    * [System Package](#system-package)
    * [Start the Service](#start-the-service)
    * [Elasticsearch inside a Docker](#elasticsearch-inside-a-docker)


## Installation Options
Cortex is available as:

- an [RPM package](#rpm)
- a [DEB package](#deb)
- a [Docker image](#docker)
- a [binary package](#binary)

In addition, Cortex can be also be [built from the source code](#build-it-yourself).

### RPM

RPM packages are published on a our RPM repository. All packages are signed using our GPG key [562CBC1C](https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY). Its fingerprint is:

`0CD5 AC59 DE5C 5A8E 0EE1 3849 3D99 BB18 562C BC1C`

Run the following command to import the GPG key :

```bash
sudo rpm --import https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY
```

---

#### Release versions

The release repository contains packages for **Cortex 3.1.0+**.



Setup your system to connect the RPM repository. Create and edit the file `/etc/yum.repos.d/thehive-project.repo`:

```bash
[thehive-project]
enabled=1
priority=1
name=TheHive-Project RPM repository
baseurl=https://rpm.thehive-project.org/release/noarch
gpgcheck=1
```

Then you will able to install  **Cortex 3.1.0+**  the package using `yum`:

```bash
yum install cortex
```

---


#### Stable versions (or legacy versions)


The main repository  is a legacy repository and contains packages for **Cortex 3.0.1** that **does not support Elasticsearch version 7.x**, but version 6.x.

Setup your system to connect the RPM repository. Create and edit the file `/etc/yum.repos.d/thehive-project.repo`:

```bash
[thehive-project]
enabled=1
priority=1
name=TheHive-Project RPM repository
baseurl=https://rpm.thehive-project.org/stable/noarch
gpgcheck=1
```

Then you will able to install **Cortex 3.0.1** package using `yum`:

```bash
yum install cortex
```

---

#### Following beta versions

To follow beta versions of Cortex, use the following setup:

And setup your system to connect the RPM repository. Create and edit the file `/etc/yum.repos.d/thehive-project.repo`:

```bash
[thehive-project]
enabled=1
priority=1
name=TheHive-Project RPM repository
baseurl=http://rpm.thehive-project.org/beta/noarch
gpgcheck=1
```

Then you will able to install the package using `yum`:

```bash
yum install cortex
```

⚠️   **We do not recommend that configuration for production servers**

Once the package is installed, [install the analyzers](#analyzers-and-responders) as outlined in the next section and proceed to the configuration using the [Quick Start Guide](../admin/quick-start.md). For more advanced configuration options, please refer to the [Administration Guide](../admin/admin-guide.md).

---


### DEB

Debian packages are published on a our DEB packages repository. All packages are signed using our GPG key [562CBC1C](https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY). Its fingerprint is:

`0CD5 AC59 DE5C 5A8E 0EE1  3849 3D99 BB18 562C BC1C`

---

#### Release versions

The release repository contains packages for **Cortex 3.1.0+**.

Setup apt configuration  with the `release` repository:

```bash
curl https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY | sudo apt-key add -
echo 'deb https://deb.thehive-project.org release main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-get update
```

Then you will able to install  **Cortex 3.1.0+**  the package using `apt` command:

```bash
apt install cortex
```

---

#### Stable versions


The main repository  is a legacy repository and contains packages for **Cortex 3.0.1** that **does not support Elasticsearch version 7.x**, but version 6.x.

Setup apt configuration  with the `main` repository:

```bash
curl https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY | sudo apt-key add -
echo 'deb https://deb.thehive-project.org stable main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-get update
```

Then you will able to install  **Cortex 3.0.1**   package using `apt` command:

```bash
apt install cortex
```

---

#### Beta versions

To follow beta versions of Cortex, use the following commands:

```bash
curl https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY | sudo apt-key add -
echo 'deb https://deb.thehive-project.org beta main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-get update
sudo apt-get install cortex
```

⚠️   **We do not recommend that configuration for production servers**

---

### Docker
To use the Docker image, you must use [Docker](https://www.docker.com/) (courtesy of Captain Obvious).

By default, the docker image generate a configuration file for Cortex with:
 - the Elasticsearch uri is determined by resolving the host name "elasticsearch",
 - the [analyzers](https://download.thehive-project.org/analyzers.json) and [responders](https://download.thehive-project.org/responders.json) official location,
 - a generated secret (used to protect the user sessions).
The behaviour of the Cortex Docker image can be customized using environment variables or parameters:

| Parameter | Env variable | Description |
| ------ | ------------ | ----------- |
| `--no-config` | `no_config=1` | Do not configure Cortex |
| `--no-config-secret` | `no_config_secret=1` | Do not add the random secret to the configuration |
| `--no-config-es` | `no_config_es=1` | do not add elasticsearch hosts to configuration
| `--es-uri <uri>` | `es_uri=<uri>` | use this string to configure elasticsearch hosts (format: http(s)://host:port,host:port(/prefix)?querystring)
| `--es-hostname <host>` | `es_hostname=host` | resolve this hostname to find elasticsearch instances
| `--secret <secret>` | `secret=<secret>` | secret to secure sessions
| `--show-secret` | `show_secret=1` | show the generated secret
| `--job-directory <dir>` | `job_directory=<dir>` | use this directory to store job files
| `--docker-job-directory <dir>` | `docker_job_directory=<dir>` | indicate the job directory in the host (not inside container)
| `--analyzer-url <url>` | `analyzer_urls=<url>,<url>,...` | where analyzers are located (url or path)
| `--responder-url <url>` | `responder_urls=<url>,<url>,...` | where responders are located (url or path)
| `--start-docker` | `start_docker=1` | start an internal docker (inside container) to run analyzers/responders
| `--daemon-user <user>` | `daemon_user=<user>` | run cortex using this user

At the end of the generated configuration, the file `/etc/cortex/application.conf` is included. Thus you can override any setting by binding your own `application.conf` into this file:
```
docker run --volume /path/to/my/application.conf:/etc/cortex/application.conf thehiveproject/cortex:3.1.0-1 --es-uri http://elasticsearch.local:9200
```

Cortex uses docker to run analyzers and responders. If you run Cortex inside a docker, you can:
 - give Cortex access to docker service (recommended solution)
 - start a docker service inside Cortex docker container

#### Cortex uses main docker service
In order to use docker service the docker socket must be bound into Cortex container. Moreover, as Cortex shares files with analyzers, a folder must be bound between them.
```
docker run --volume /var/run/docker.sock:/var/run/docker.sock --volume /var/run/cortex/jobs:/tmp/cortex-jobs thehiveproject/cortex:3.1.0-1 --job-directory /tmp/cortex-jobs --docker-job-directory /var/run/cortex/jobs
```
Cortex can instantiate docker container by using the docker socket `/var/run/docker.sock`. The folder `/var/run/cortex/jobs` is used to store temporary file of jobs. The folder `/tmp/cortex-jobs` is job folder inside the docker. In order to make job file visible to analyzer docker, Cortex needs to know both folders (parameters `--job-directory` and `-docker-job-directory`). On most cases, job directories are the same and `--docker-job-directory` can be omitted.

If you run Cortex in Windows, the docker service is accessible through the named pipe `\\.\pipe\docker_engine`. The command becomes
```
docker run --volume //./pipe/docker_engine://./pipe/docker_engine --volume C:\\CORTEX\\JOBS:/tmp/cortex-jobs thehiveproject/cortex:3.1.0-1 --job-directory /tmp/cortex-jobs --docker-job-directory C:\\CORTEX\\JOBS
```

#### Docker in docker (docker-ception)
You can also run docker service inside Cortex container, a docker in a docker with `--start-docker` parameter. The container must be run in privileged mode.
```
docker run --privileged thehiveproject/cortex:3.1.0-1 --start-docker
```
In this case you don't need to bind job directory.

#### Use Docker-compose
Cortex 2 and newer requires [Elasticsearch](#elasticsearch-inside-a-docker) to run. You can use `docker-compose` to start them together in Docker or install and configure Elasticsearch manually.
[Docker-compose](https://docs.docker.com/compose/install/) can start multiple dockers and link them together.

The following [docker-compose.yml](https://raw.githubusercontent.com/TheHive-Project/Cortex/master/docker/cortex/docker-compose.yml)
file starts Elasticsearch and Cortex:
```
version: "2"
services:
  elasticsearch:
    image: elasticsearch:7.9.1
    environment:
      - http.host=0.0.0.0
      - discovery.type=single-node
      - script.allowed_types=inline
      - thread_pool.search.queue_size=100000
      - thread_pool.write.queue_size=10000
    volumes:
      - /path/to/data:/usr/share/elasticsearch/data
  cortex:
    image: thehiveproject/cortex:3.1.1
    environment:
      - job_directory=${job_directory}
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ${job_directory}:${job_directory}
    depends_on:
      - elasticsearch
    ports:
      - "0.0.0.0:9001:9001"
```

Put this [docker-compose file](https://raw.githubusercontent.com/TheHive-Project/Cortex/master/docker/docker-compose.yaml) and [.env](https://raw.githubusercontent.com/TheHive-Project/Cortex/master/docker/cortex/.env) in an empty folder and run `docker-compose up`. Cortex is exposed on 9001/tcp port. These ports can be changed by modifying the `docker-compose` file.

You can also use TheHive [docker-compose](https://raw.githubusercontent.com/TheHive-Project/TheHive/master/docker/thehive/docker-compose.yml) file which contains TheHive, Cortex and Elasticsearch, as documented in [TheHive's Docker installation instructions](https://github.com/TheHive-Project/TheHiveDocs/blob/master/installation/docker-guide.md).


You should define where the data (i.e. the Elasticsearch database) will be located on your operating system by adding the following lines in the `elasticsearch` section of your docker-compose file:
```
volumes:
    - /path/to/data:/usr/share/elasticsearch/data
```

#### Analyzers and responders
Neurons (analyzers and responders) are not embedded anymore in docker image. The recommended method to run neurons is to use docker. Official neurons have their own image is dockerhub under cortexneurons organisation. The catalog of available neurons is provided to Cortex using `--analyzer-url`/`--responder-url` parameters.

You can still use legacy method (process) but you must ensure that Neuron files are accessible by Cortex and all the requirements (library dependencies) of your neurons are met.
`docker run -v /path/to/your/analyzers:/opt/cortex/analyzers thehiveproject/cortex:latest --analyzer-urls /opt/cortex/analyzers`

In order to add dependencies, you need to extends docker image with your own dockerfile:
```
FROM thehiveproject/cortex:3.1.0-1

USER root
RUN apt update && apt install -y python3-pip && rm -rf /var/lib/apt/lists/*
RUN pip3 install ldap3 requests
USER daemon
```

#### What to Do Next?
Once the Docker image is up and running, proceed to the configuration using the [Quick Start Guide](../admin/quick-start.md). For more advanced configuration options, please refer to the [Administration Guide](../admin/admin-guide.md).

#### Pre-release Versions
If you would like to use pre-release, beta versions of our Docker images and help us find bugs to the benefit of the whole community, please use `thehiveproject/cortex:version-RCx`. For example `thehiveproject/cortex:3.1.0-0.1RC1`.

### Binary
The following section contains the instructions to manually install Cortex using binaries on **Ubuntu**.

#### 1. Minimal Ubuntu Installation
Install a minimal Ubuntu 20.04 system with the following software:

- Java runtime environment 1.8+ (JRE)
- Elasticsearch 7.x for Cortex 3.1

Make sure your system is up-to-date:

```
sudo apt-get update
sudo apt-get upgrade
```

#### 2. Install a Java Virtual Machine
You can install either Oracle Java or OpenJDK.

##### 2.1. Oracle Java
```
echo 'deb http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main' | sudo tee -a /etc/apt/sources.list.d/java.list
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key EEA14886
sudo apt-get update
sudo apt-get install oracle-java11-installer
```

##### 2.2 OpenJDK
```
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install openjdk-11-jre-headless

```

#### 3. Install Elasticsearch
To install Elasticsearch, please read the [Elasticsearch Installation](#elasticsearch-installation) section below.

#### 4. Install Cortex
Binary packages can be downloaded from TheHive-Project repository. The latest version is called [cortex-latest.zip](https://download.thehive-project.org/cortex-latest.zip).

Download and unzip the chosen binary package. Cortex files can be installed wherever you want on the filesystem. In this guide, we assume you have chosen to install them under `/opt`.

```
cd /opt
wget https://download.thehive-project.org/cortex-latest.zip
unzip cortex-latest.zip
ln -s cortex-x.x.x cortex
```

**Note**: if you would like to use pre-release, beta versions of and help us find bugs to the benefit of the whole community, please download `https://download.thehive-project.org/cortex-version-RCx.zip`. For example `https://download.thehive-project.org/cortex-2.1.0-RC1.zip`.

#### 5. First start
It is recommended to use a dedicated, non-privileged user account to start Cortex. If so, make sure that the chosen account can create log files in `/opt/cortex/logs`.

If you'd rather start the application as a service, use the following commands:
```
sudo addgroup cortex
sudo adduser --system cortex
sudo cp /opt/cortex/package/cortex.service /usr/lib/systemd/system
sudo chown -R cortex:cortex /opt/cortex
sudo chgrp cortex /etc/cortex/application.conf
sudo chmod 640 /etc/cortex/application.conf
sudo systemctl enable cortex
sudo service cortex start
```
The only required parameter in order to start Cortex is the key of the server (`play.http.secret.key`). This key is used
to authenticate cookies that contain data. If Cortex runs in cluster mode, all instances must share the same key.
You can generate the minimal configuration with the following commands (they assume that you have created a
dedicated user for Cortex, named `cortex`):

```
sudo mkdir /etc/cortex
(cat << _EOF_
# Secret key
# ~~~~~
# The secret key is used to secure cryptographics functions.
# If you deploy your application to several instances be sure to use the same key!
play.http.secret.key="$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 64 | head -n 1)"
_EOF_
) | sudo tee -a /etc/cortex/application.conf
```

Now you can start Cortex. To do so, change your current directory to the Cortex installation directory (`/opt/cortex` in this guide), then execute:

```
bin/cortex -Dconfig.file=/etc/cortex/application.conf
```

Please note that the service may take some time to start. Once it is started, you may launch your browser and connect to `http://YOUR_SERVER_ADDRESS:9001/`.

#### 6. Plug Analyzers and Responders
Now that Cortex has successfully started, download `Cortex-Analyzers`, edit the configuration file then set the analyzer path to
`Cortex-Analyzers/analyzers` and `Cortex-Analyzers/responders` as described in [the section below](#analyzers-and-responders).

#### 7. Update
To update Cortex from binaries, just stop the service, download the latest package, rebuild the link `/opt/cortex` and
restart the service.

```
service cortex stop
cd /opt
wget https://download.thehive-project.org/cortex-latest.zip
unzip cortex-latest.zip
rm /opt/cortex && ln -s cortex-x.x.x cortex
chown -R cortex:cortex /opt/cortex /opt/cortex-x.x.x
service cortex start
```

#### 8. Configuration
To configure Cortex, read the [Quick Start Guide](../admin/quick-start.md). For more advanced configuration options, please refer to the [Administration Guide](../admin/admin-guide.md).

### Build it Yourself
The following section contains a step-by-step guide to build Cortex from its sources.

#### 1. Pre-requisites
The following software are required to download and build Cortex:

* [Java Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* git: use the system package or [download it](http://www.git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) with its package manager (NPM)
* [Elasticsearch 5.6](https://www.elastic.co/downloads/past-releases/elasticsearch-5-6-9)

##### 2. Build
To install the requirements and build Cortex from sources, please follow the instructions below depending on your operating system.

###### 2.1. CentOS/RHEL

**Packages**

```
sudo yum -y install git bzip2
```

**Installation of OpenJDK**

```
sudo yum -y install java-1.8.0-openjdk-devel
```

**Installation of Node.js**

Install the EPEL repository. You should have the *extras* repository enabled, then:  
```
sudo yum -y install epel-release
```

Then, you can install Node.js:

```
sudo yum -y install nodejs
```

**Installation of Elasticsearch**

To install Elasticsearch, please read the [Elasticsearch Installation](#elasticsearch-installation) section below.

###### 2.2. Ubuntu

**Packages**

```
sudo apt-get install git wget
```

**Installation of Oracle JDK**

```
echo 'deb http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main' | sudo tee -a /etc/apt/sources.list.d/java.list
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key EEA14886
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

**Installation of Node.js**

```
sudo apt-get install wget
wget -qO- https://deb.nodesource.com/setup_8.x | sudo bash -
sudo apt-get install nodejs
```

**Installation of Elasticsearch**

To install Elasticsearch, please read the [Elasticsearch Installation](#elasticsearch-installation) section below.

###### 2.3. Cortex
**Download The Source**

```
git clone https://github.com/TheHive-Project/Cortex.git
```

**Build the Project**

```
cd Cortex
./sbt clean stage
```

This operation may take some time to complete as it will download all dependencies then build the back-end.
This command cleans any previous build files and create an autonomous package under the `target/universal/stage` directory. This package contains Cortex binaries with the required libraries (`/lib`), configuration files (`/conf`) and startup scripts (`/bin`).

Binaries are built and stored under `Cortex/target/universal/stage/`. You can install them in `/opt/cortex` for example.

```
sudo cp -r Cortex/target/universal/stage /opt/cortex
```

Proceed to [installing the analyzers](#analyzers-and-responders) as outlined in the next section and configure Cortex using the [Quick Start Guide](../admin/quick-start.md). For more advanced configuration options, please refer to the [Administration Guide](../admin/admin-guide.md).

##### 2.4 Configure and Start Elasticsearch

Edit `/etc/elasticsearch/elasticsearch.yml` and add the following lines:

```
http.host: 127.0.0.1
cluster.name: hive
thread_pool.search.queue_size: 100000
```

Start the service:

```
service elasticsearch restart
```

##### 3. First start
Follow the [first start](#5-first-start) section of the binary installation method above to start using Cortex.

##### 4. Build the Front-end Only
Building the back-end builds also the front-end, so you don't need to build it separately. This section is useful only for troubleshooting or for installing the front-end on a reverse proxy.

Go to the front-end directory:
```
cd Cortex/www
```

Install Node.js libraries, which are required by this step, bower libraries (JavaScript libraries downloaded by the browser). Then
build the front-end :
```
npm install --ignore-scripts
npm run build
```

This step generates static files (HTML, JavaScript and related resources) in  the `dist` directory. They can be readily imported on a HTTP server.

## Analyzers and Responders
Analyzers and Responders are autonomous applications managed by and run through the Cortex core engine. They have their
[own dedicated GitHub repository](https://github.com/TheHive-Project/Cortex-Analyzers).
They are included in the Docker image but must be installed separately if you are using binary, RPM or DEB packages.

### Installation
Currently, all the analyzers and responders supported by TheHive Project are written in Python 2 or 3. They don't require any build phase but their dependencies have
to be installed. Before proceeding, you'll need to install the system package dependencies that are required by some of them:

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

Each analyzer comes with its own, pip compatible `requirements.txt` file. You can install all requirements with the following commands:

```
for I in $(find Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip2 install -r $I; done && \
for I in $(find Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip3 install -r $I || true; done
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

responder {
  # Directory that holds responders
  path = [
    "/path/to/default/responder",
    "/path/to/my/own/responder"
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
### Configuration
All analyzers and responders must be configured using the Web UI. Please read the [Quick Start Guide](../admin/quick-start.md) to create at least one organization then let a user with the `orgAdmin` role configure and enable analyzers for that organization.

Some analyzers can be used out of the box, without any configuration, while others may require various parameters. Please check the [Analyzer Requirements Guide](../analyzer_requirements.md) for further details.

### Updating
Existing Cortex analyzers are regularly updated and new ones are added. To benefit from the latest bug fixes, enhancements and additions, run the following commands (we assume that you have installed them under `/opt/Cortex-Analyzers`):

```bash
$ cd /opt/Cortex-Analyzers
$ sudo git pull
```
Then install any missing requirements:

```bash
for I in $(find /opt/Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip2 install -U -r $I; done && \
for I in $(find /opt/Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip3 install -U -r $I || true; done
```
After running these commands, read the Analyzer Requirements Guide,  log into the Cortex 2 Web UI as an `orgAdmin`, click on the Refresh Analyzers button in the Cortex Web UI, configure the new analyzers and enjoy!

If you are using TheHive, get the [latest version of the report templates](https://download.thehive-project.org/report-templates.zip) and import them into TheHive.

### Additional Analyzers
The following analyzers are not supported by TheHive Project at this time:

- [Analyzers written in Go](https://github.com/Rostelecom-CERT/go-cortex-analyzers) by Rosetelecom-CERT

## Elasticsearch Installation
If, for some reason, you need to install Elasticsearch, it can be installed using a system package or a Docker image. The latter is preferred as its installation and update are easier.

### System Package
Install the Elasticsearch package provided by Elastic:
```
# PGP key installation
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key D88E42B4

# Alternative PGP key installation
# wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

# Debian repository configuration
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

# Install https support for apt
sudo apt install apt-transport-https

# Elasticsearch installation
sudo apt update && sudo apt install elasticsearch
```

The Debian package does not start up the service by default,  to prevent the instance from accidentally joining a cluster, without being configured appropriately.

If you prefer using Elasticsearch inside a docker, see
[Elasticsearch inside a Docker](#elasticsearch-inside-a-docker).

#### Configuration
It is **highly recommended** to avoid exposing this service to an untrusted zone.

If Elasticsearch and Cortex run on the same host (and not in a docker), edit `/etc/elasticsearch/elasticsearch.yml` and
set `network.host` parameter with `127.0.0.1`. Cortex use dynamic scripts to make partial updates. Hence, they must be activated using `script.inline: on`.

The cluster name must also be set (`hive` for example). Threadpool queue size must be set with a high value (`100000`). The default size will get the queue easily overloaded.

Edit `/etc/elasticsearch/elasticsearch.yml` and add the following lines:

```
http.host: 127.0.0.1
cluster.name: hive
thread_pool.search.queue_size: 100000
```

### Start the Service
Now that Elasticsearch is configured, start it as a service and check whether it's running:
```
sudo systemctl enable elasticsearch.service
sudo systemctl start elasticsearch.service
sudo systemctl status elasticsearch.service
```

The status should be `active (running)`. If it's not running, you can check for the reason in the logs:
```
sudo journalctl -u elasticsearch.service
```

Note that by default, the database is stored in `/var/lib/elasticsearch` and the logs in `/var/log/elasticsearch`

### Elasticsearch inside a Docker
You can also start Elasticsearch inside a docker. Use the following command and do not forget to specify the absolute path for persistent data on your host :

```
docker run \
  --name elasticsearch \
  --hostname elasticsearch \
  --rm \
  --publish 127.0.0.1:9200:9200 \
  --volume ***DATA_DIR***:/usr/share/elasticsearch/data \
	-e "http.host=0.0.0.0" \
	-e "xpack.security.enabled=false" \
	-e "cluster.name=hive" \
  -e "script.inline=true" \
  -e "thread_pool.search.queue_size=100000" \
	docker.elastic.co/elasticsearch/elasticsearch:7.9.1
```

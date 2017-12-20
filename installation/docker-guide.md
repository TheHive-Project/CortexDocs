# Cortex Installation Using Docker

This guide assume that you will use [Docker](https://www.docker.com/).

## How to Use the Docker Image

The easiest way to start Cortex is the following one:
```
docker run certbdf/cortex
```

Starting from Cortex version 1.1.0, we no longer provide the all-in-one docker (an image which contained TheHive and Cortex). If you want to
run TheHive and Cortex using Docker, follow the
[TheHive Docker Guide](https://github.com/TheHive-Project/TheHiveDocs/blob/master/installation/docker-guide.md).

## Analyzers

Analyzers are embedded in the docker image in `/opt/Cortex-Analyzers/analyzers`. If you want to update them, you should
install them outside docker and overwrite the existing ones:
```
docker run --volume /path/to/analyzers:/opt/Cortex-Analyzers/analyzers:ro certbdf/cortex:latest  
```

Most analyzers require configuration. You can inject a configuration file using the `volume` argument:
```
docker run --volume /path/to/your/configuration:/etc/cortex/application.conf:ro certbdf/cortex:latest  
```

You should also publish HTTP service to make Cortex reachable. This is done by adding the `publish` parameter:
```
docker run --publish 0.0.0.0:8080:9000 certbdf/cortex:latest  
```
This command exposes the Cortex service on port 8080/tcp.

## Customize the Image

By Default, the Cortex docker image adds minimal configuration:
 - choose a random secret (play.crypto.secret)
 - configure analyzer path

This behavior can be disabled by adding `--no-config` to the docker command line:
`docker run certbdf/cortex:latest --no-config`.
 
The Docker image accepts more options:
 - --no-config            : do not try to configure Cortex (add secret and analyzers location)
 - --no-config-secret     : do not add random secret to configuration
 - --secret <secret>      : secret to secure sessions
 - --analyzer-path <path> : where analyzers are located
 - --no-misp-modules      : disabled MISP modules



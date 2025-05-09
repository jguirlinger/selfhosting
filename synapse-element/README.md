# About

<p align="center">
<img src="../_utilities/matrix.png" width="400" alt="matrix" title="matrix" /> 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="../_utilities/element.png" width="400" alt="pihole" title="pihole" />
</p>

Matrix is an open standard for decentralised communication, which securely distributes persistent chatrooms over an open federation of servers preventing any single points of control or failure.

Synapse is a Matrix "homeserver" implementation developed by the matrix.org core team.

Element is a Matrix web client built using the Matrix React SDK.

* Synapse
  * [Github](https://github.com/matrix-org/synapse)
  * [Documentation](https://matrix-org.github.io/synapse/latest/)
  * [Docker Image](https://hub.docker.com/r/matrixdotorg/synapse)

* Element
  * [Github](https://github.com/vector-im/element-web)
  * [Docker Image](https://hub.docker.com/r/vectorim/element-web)

# Table of Contents

<!-- TOC -->

- [About](#about)
- [Table of Contents](#table-of-contents)
- [Files structure](#files-structure)
- [Information](#information)
    - [docker-compose](#docker-compose)
- [Usage](#usage)
    - [Requirements](#requirements)
    - [Configuration](#configuration)
- [Update](#update)
- [Security](#security)
- [Backup](#backup)

<!-- /TOC -->


# Files structure 

```bash
.
|-- docker-compose.yml
|-- matrix-data/
|-- element-web/config.json
```

- `docker-compose.yml` - a docker-compose file, use to configure your application’s services
- `matrix-data/` - a directory used to store the matrix data
- `element-web/config.json` - a file used to store element's data

Please make sure that all the files and directories are present.

# Information

## docker-compose
Links to the following [docker-compose.yml](docker-compose.yml).

* docker-compose.yml
  ```yaml
  version: '3'

  services:

    synapse:
      image: matrixdotorg/synapse:latest
      restart: unless-stopped
      container_name: synapse
      environment:
        - SYNAPSE_LOG_LEVEL=INFO
      volumes:
        - ./matrix-data:/data
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.synapse.rule=Host(`${TRAEFIK_MATRIX}`)"
        - "traefik.http.routers.synapse.entrypoints=https"
        - "traefik.http.routers.synapse.tls=true"
        - "traefik.http.routers.synapse.tls.certresolver=mytlschallenge"

        # Watchtower Update
        - "com.centurylinklabs.watchtower.enable=true"
      networks:
        - proxy

    element:
      image: vectorim/element-web:latest
      restart: unless-stopped
      container_name: element
      depends_on:
        - synapse
      volumes:
        - ./element-web/config.json:/app/config.json:ro
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.element.rule=Host(`${TRAEFIK_ELEMENT}`)"
        - "traefik.http.routers.element.entrypoints=https"
        - "traefik.http.routers.element.tls=true"
        - "traefik.http.routers.element.tls.certresolver=mytlschallenge"

        # Watchtower Update
        - "com.centurylinklabs.watchtower.enable=true"
      networks:
        - proxy


  networks:
     proxy:
      external: true
  ```
* .env
  ```ini
  TRAEFIK_MATRIX=matrix.example.com
  TRAEFIK_ELEMENT=element.example.com
  ```


# Usage

## Requirements
- [Traefik up and running](../traefik).
- Subdomains of your choice, this example uses `element` and `matrix`.
    - You should be able to create a subdomain with your DNS provider, use a `A record` with the same IP address as your root domain.

## Configuration

Check that the URL is correct in `element-web/config.json` and replace the environment variables in `.env` with your own.

The first step is to generate a valid config file. To do this, you can run the image with the generate command line option. Please replace the `SYNAPSE_SERVER_NAME` with your own.

```bash
sudo docker run -it --rm \
    -v "$(pwd)/matrix-data:/data" \
    -e SYNAPSE_SERVER_NAME=matrix.example.com \
    -e SYNAPSE_REPORT_STATS=no \
    matrixdotorg/synapse:latest generate
```

You can now start the service with :

```bash
sudo docker-compose up -d
```

You can check that the matrix server is working by accessing the URL. You should then be able to access the element web-ui and create a new account. By default, the registration is disabled, change the `enable_registration` value to `true` in `matrix-data/homeserver.yaml`, then restart the container.

# Update

The image is automatically updated with [watchtower](../watchtower) thanks to the following label :

```yaml
  # Watchtower Update
  - "com.centurylinklabs.watchtower.enable=true"
```

# Security

You can modify the matrix config file to increase security : `matrix-data/homeserver.yaml`.
After you have created your account, you can disable registration with `enable_registration: false`.



# Backup

Docker volumes are globally backed up using [borg-backup](../borg-backup). 
# About

<p align="center">
<img src="../_utilities/freshrss.png" alt="freshrss" title="freshrss" />
</p>

FreshRSS is a self-hosted RSS feed aggregator

* [Github](https://github.com/FreshRSS/FreshRSS)
* [Documentation](https://freshrss.github.io/FreshRSS/en/admins/01_Index.html)
* [Docker Image](https://hub.docker.com/r/linuxserver/freshrss)

# Table of Contents

<!-- TOC -->

- [About](#about)
- [Table of Contents](#table-of-contents)
- [Information](#information)
    - [docker-compose](#docker-compose)
- [Usage](#usage)
    - [Requirements](#requirements)
    - [Configuration](#configuration)
- [Update](#update)
- [Backup](#backup)

<!-- /TOC -->

```bash
.
|-- .env
|-- data/
`-- docker-compose.yml
```

* `.env` - a file containing all the environment variables used in the docker-compose.yml
* `docker-compose.yml` - a docker-compose file, use to configure your application’s services
* `data/` - a directory used to store the data

Please make sure that all the files and directories are present.

# Information

##  docker-compose

Links to the following [docker-compose.yml](docker-compose.yml) and the corresponding [.env](.env).

# Usage

## Requirements

* [Traefik up and running](../traefik).
* A subdomain of your choice, this example uses `freshrss`.
  * You should be able to create a subdomain with your DNS provider, use a `A record` with the same IP address as your root domain.

## Configuration

Replace the environment variables in `.env` with your own, then run :

```bash
sudo docker-compose up -d
```

You should now be able to access the freshrss setup instruction, it is quite straigthforward and nothing is required. 

# Update

The image is automatically updated with [watchtower](../watchtower) thanks to the following label :

```yaml
  # Watchtower Update
  - "com.centurylinklabs.watchtower.enable=true"
```

# Backup

Docker volumes are globally backed up using [borg-backup](../borg-backup).

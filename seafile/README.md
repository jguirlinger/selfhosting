# About

<p align="center">
<img src="../_utilities/seafile.png" alt="seafile" title="seafile" />
</p>

Seafile is an open-source, cross-platform file-hosting software system. Files are stored on a central server and can be synchronized with personal computers and mobile devices through apps or through the web interface.

* [Github](https://github.com/haiwen/seafile)
* [Documentation](https://manual.seafile.com/docker/deploy_seafile_with_docker/)
* [Docker Image](https://hub.docker.com/r/seafileltd/seafile-mc)

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
|-- .env
|-- docker-compose.yml
|-- seafile-mysql/
`-- shared/
```

- `.env` - a file containing all the environment variables used in the docker-compose.yml
- `docker-compose.yml` - a docker-compose file, use to configure your application’s services
- `seafile-mysql/` - a directory used to store the mysql data
- `shared/` - a directory used to store seafile's data

Please make sure that all the files and directories are present.

# Information

## docker-compose
Links to the following [docker-compose.yml](docker-compose.yml) and the corresponding [.env](.env).

# Usage

## Requirements
- [Traefik up and running](../traefik).
- A subdomain of your choice, this example uses `seafile`.
    - You should be able to create a subdomain with your DNS provider, use a `A record` with the same IP address as your root domain.

## Configuration

Replace the environment variables in `.env` with your own, then run :

```bash
sudo docker-compose up -d
```

You should then be able to access the seafile web-ui with the SEAFILE_ADMIN_EMAIL and SEAFILE_ADMIN_PASSWORD.

# Update

The image is automatically updated with [watchtower](../watchtower) thanks to the following label :

```yaml
  # Watchtower Update
  - "com.centurylinklabs.watchtower.enable=true"
```

# Security

Seafile provides client-side end-to-end data encryption. You can create encrypted libraries to use this feature. Use this feature to add extra security to your documents.

# Backup

Docker volumes are globally backed up using [borg-backup](../borg-backup). 
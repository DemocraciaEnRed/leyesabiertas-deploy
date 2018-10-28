# DemocracyOS On-Premises Ansible Role
Installation and deployment of DemocracyOS with Docker and Ansible.

## Description
This role is used to install and run:

* Docker with docker-compose.
* DemocracyOS docker-compose deployment.

**IMPORTANT**: this role has tasks grouped by tags, please read the tags section in Role Variables description below!

## Requirements
* SSH access to your instance/s **must** be configured since we're using Ansible. It is strongly recommended to use certificate based access, a complete guide can be found [here](https://wiki.archlinux.org/index.php/SSH_keys). Also, using a SSH config file is a really good practice, guide [here](https://www.digitalocean.com/community/tutorials/how-to-configure-custom-connection-options-for-your-ssh-client).
* Ansible 2.2 or greater is required in administrator's machine, installation guide is [here](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).
* Clone this repository in your roles\_path, this should be `/etc/ansible/roles` on Linux or usually `~/.ansible/roles` on MacOS.
* Configure your [Ansible Inventory](https://docs.ansible.com/ansible/2.3/intro_inventory.html) with the managed instance/s where DemocracyOS will be deployed. Example:

```
[production]
server1    ansible_ssh_host=server1.example.com    ansible_connection=ssh    ansible_port=22    ansible_user=admin
```
* **IMPORTANT**: Ubuntu 16.04 and newer need `python-minimal` package installed, since python 2 is not installed by default.

## Supported platforms
Deployment is based on [Docker](https://www.docker.com/) and [docker-compose](https://docs.docker.com/compose/) so we only support platforms that fill Docker requirements. Note that **Docker installation is optional** setting docker\_install to false, so this role might be used with existing systems. This role is tested with Vagrant using official boxes only:

* CentOS 7 (7.3, 7.4 and 7.5).
* Debian 8.
* Ubuntu 14.04, 16.04, 17.10.

_NOTE: docker-ce stable for Ubuntu 18.04 was released at the time of this writing and it will be tested shortly._
Additional information can be found in tests/README.md.

## Role Variables
[DemocracyOS Configuration](http://docs.democracyos.org/configuration.html) has an extensive list of environment variables that can be set to configure the application instance. Nevertheless a set of this variables are set as Ansible variables because they are needed to make decisions and template files. The rest of configuration variables are set with a special variable named `democracyos`, which is a YAML dictionary, please note that these **must not** be included:
* `PROTOCOL`
* `HOST`
* `PUBLIC_PORT`
* `MONGO_URL`
* `JWT_SECRET`
* `HTTPS_REDIRECT`

You can set the rest of them, e.g.:
* `ORGANIZATION_NAME`
* `LOCALE`
* `STAFF`
Etc.

**DemocracyOS Services Docker Images**

This role installs the next services created/implemented by DemocracyOS as Docker Images:

* **DemocracyOS application**: a list of versions can be found [here](https://hub.docker.com/r/democracyos/democracyos/tags/).
* **DemocracyOS Core API Service**: a list of versions can be found [here](https://hub.docker.com/r/democracyos/core/tags/).
* **DemocracyOS Keycloak Service**: a list of versions can be found [here](https://hub.docker.com/r/democracyos/keycloak/tags/).
* **DemocracyOS Notifier Service**: a list of versions can be found [here](https://hub.docker.com/r/democracyos/notifier/tags/).

These services have no default version set as they can be updated with this role at any given time.  

Available Ansible variables and its default values:

| Variable                           | Default Value                                     | Usage                                    |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `docker_compose_version`           | `1.21.2`                                          | Select docker-compose binary version.    |
| `docker_install`                   | `true`                                            | Choose to install Docker.                |
| `enable_own_certificate`           | `false`                                           | Choose to use certificate and key files. |
| `https_certificate_path`           | `undefined`                                       | Absolute path to certificate file.       |
| `https_key_path`                   | `undefined`                                       | Absolute path to key file.               |
| `enable_lets_encrypt`              | `false`                                           | Choose to use Let's Encrypt.             |
| `enable_lets_encrypt_staging`      | `true`                                            | Use Let's Encrypt Staging CA Server.     |
| `lets_encrypt_email`               | `undefined`                                       | Let's Encrypt email for domain.          |
| `install_dir_path`                 | `/opt/democracy_os`                               | Deployment path.                         |
| `docker_volumes_path`              | `{{ install_dir_path}}/docker_volumes`            | Docker volumes path.                     |
| `enable_external_mongo`            | `false`                                           | Use external MongoDB Server.             |
| `mongo_host`                       | `mongo`                                           | MongoDB Server host.                     |
| `mongo_port`                       | `27017`                                           | MongoDB Server port.                     |
| `mongodb_backup_storage_dir_path`  | `{{ docker_volumes_path }}/mongo_backup_storage`  | MGOB storage volume path.                |
| `mongodb_backup_config_dir_path`   | `{{ docker_volumes_path }}/mongo_backup_config`   | MGOB configuration volume path.          |
| `mongodb_backup_tmp_dir_path`      | `{{ docker_volumes_path }}/mongo_backup_tmp`      | MGOB /tmp volume path.                   |
| `mongodb_backup_data_dir_path`     | `{{ docker_volumes_path }}/mongo_backup_data`     | MGOB data volume path.                   |
| `mongodb_volume_dir_path`          | `{{ docker_volumes_path }}/mongo_container`       | MongoDB container volume path.           |
| `enable_external_mysql`            | `false`                                           | Use external MySQL Server.               |
| `mysql_host`                       | `mysql`                                           | External MySQL Server host.              |
| `mysql_port`                       | `3306`                                            | External MySQL Server port.              |
| `traefik_config_dir_path`          | `{{ docker_volumes_path }}/traefik`               | Traefik container volume path.           |
| `democracyos_docker_image`         | `undefined`                                       | DemocracyOS Application Docker image.    |
| `democracyos_core_image`           | `undefined`                                       | DemocracyOS Core API Docker image.       |
| `democracyos_keycloak_image`       | `undefined`                                       | DemocracyOS Keycloak Docker image.       |
| `democracyos_notifier_image`       | `undefined`                                       | DemocracyOS Notifier Docker image.       |    
| `democracyos_database_name`        | `democracyos`                                     | DemocracyOS MongoDB database name.       |
| `democracyos_protocol`             | `http`                                            | Protocol to be used for URL building.    |
| `democracyos_host`                 | `localhost`                                       | Hostname application expects.            |
| `democracyos_public_port`          | `3000`                                            | Port to be exposed in container.         |
| `democracyos_jwt_secret`           | `random generated value`                          | [JSON Web Token](https://jwt.io)         |
| `enable_mgob`                      | `false`                                           | Install MGOB to manage MongoDB backups.  |

## External Services
You may choose to use external servers for MySQL and MongoDB. If that is the case then, for MongoDB the next variables must be defined:

* `enable_external_mogno`: `true`
* `mongo_host`: MongoDB server hostname or IPv4 address.
* `mongo_port`: MongoDB server port (defaults to 27017)

For MySQL:

* `enable_external_mysql`: `true`
* `mysql_host`: MySQL server hostname or IPv4 address.
* `mysql_port`: MySQL server port (defaults to 3306).
* `mysql_user`: A user **must** exist on the server.
* `mysql_password`: Given user **must** have its password configured.
* `mysql_database`: A database **must** exist and `mysql_user` must have all privileges set on it.

## Docker Installation

There's a special variable called `docker_install`, its default value is `true`, meaning that latest Docker CE stable version is installed in target host/s. Set it to `false` if you already have Docker in place.

## Tags

This role has tasks grouped by tags, specifically:

* fresh\_install
* change\_version

#### Fresh Install Deployment
A fresh install means:

1. Install Docker CE (if `docker_install` is not set to `false`).
2. Check existence of and install docker-compose.
3. Download and run needed containers.

Example:

```bash
$ sudo git clone -b refactor https://github.com/DemocracyOS/onpremises /etc/ansible/roles/onpremises

# Example playbook below:
# playbook.yml
- hosts: server1
  roles:
    role: onpremises
      democracyos_docker_image: "democracyos/democracyos:2.11.0"
      democracyos_core_image: "democracyos/core:development"
      democracyos_keycloak_image: "democracyos/keycloak:4.4.0.Final"
      democracyos_notifier_image: "democracyos/notifier: development"
      democracyos_host: www.example.com
      democracyos:
        ORGANIZATION_NAME: Example

# End of playbook.yml

# Now we run our playbook
# --ask-become-pass prompts for sudoer password!
$ ansible-playbook playbook.yml --tags "fresh_install" --ask-become-pass
```

How is the deployment directory looking now?

```
/opt/democracy_os
-- docker-compose.yml
-- docker-volumes/
---- mongo_backup_storage/
---- mongo_backup_config/
---- mongo_backup_tmp/
---- mongo_backup_data/
---- mongo_container/
---- traefik/
```

#### Change Version Deployment
Changing version means:

1. Downloading requested container image, older or newer.
2. Run _new_ container and check that docker-compose return code is 0.
3. If everything _is fine_ stop _old_ container.

Example:

```bash
# This is our new playbook.yml
# Keep your old variables!
# playbook.yml
- hosts: server1
  roles:
    role: onpremises
      democracyos_docker_image: "democracyos/democracyos:2.11.7"
      democracyos_host: www.example.com
      democracyos:
        ORGANIZATION_NAME: Example

# End of playbook.yml

# Now we run our playbook
$ ansible-playbook playbook.yml --tags "change_version" --ask-become-pass
```

How is the deployment directory looking now?

```
/opt/democracy_os
-- docker-compose-app.SOME-TIMESTAMP.yml
-- docker-compose-app.yml
-- docker-volumes/
---- mongo_backup_storage/
---- mongo_backup_config/
---- mongo_backup_tmp/
---- mongo_backup_data/
---- mongo_container/
---- traefik/
```

## HTTP/HTTPS routing and load-balancing with Traefik. MGOB API.

### Traefik

[Traefik](https://traefik.io/) is a reverse proxy / load balancer written in [Go](https://golang.org/) that allows several features of this deployment. This role uses a combination of static and dynamic configuration. Entrypoints, HTTPS certificate and key (if used) and Let's Encrypt integration (if used) are part of static configuration with Ansible template module. Frontends and backends are part of dynamic configuration with Docker container labels. To better understand this terminology, refer to [Traefik Documentation](https://docs.traefik.io).

Available services with Traefik:

* HTTP Entrypoints (available by default):
  * Traefik listens on 127.0.0.1:8080 to allow access to its dashboard as this must not be publicly available.
  * Traefik listens on 127.0.0.1:8000 and forwards requests to MGOB API (read below), this must not be publicly available either.
  * Traefik listens on 0.0.0.0:80 and redirects requests to HTTPS Entrypoint **only when** `democracyos_protocol` is set to `https` and `democracyos_host` is defined in request Host Header.
* HTTPS Entrypoint: Traefik listens on 0.0.0.0:443 **only when** `democracyos_protocol` is set to `https`. Requests with Host Header set to `democracyos_host` are forwarded to available backends.

**HTTPS with certificate and key file**

If `democracyos_protocol` is set to `https` and `enable_own_certificate` is set to `true`, then you must provide absolute paths to your certificate and key file with `https_certificate_path` and `https_key_path` in order to pass these files to Traefik (both mounted as docker volumes).

**HTTPS with Let's Encrypt**

[Let's Encrypt](https://letsencrypt.org) is natively integrated in traefik and is extremely and absolutely easy to set up. If `democracyos_protocol` is set to `https` and `enable_lets_encrypt` is set to `true`, then you must provide an email address with `lets_encrypt_email` variable. That is all given that DNS name and resolution has previously been configured externally.

### MGOB
If you choose to use it, MongoDB backup automation is performed with [MGOB](https://github.com/stefanprodan/mgob) which is another excellent Go written piece of software. By default is configured to backup `democracyos_database_name` at 23:59 everyday, keep 10 backups locally and timeout at 60 seconds. This behavior is set on its template `templates/democracyos.yml.j2`.

A default installation includes the next routes, **available only** through localhost interface, you can use `ssh -L 8000:127.0.0.1:8000 user@server1` to access localhost services with your browser, more about port forwarding with SSH [here](https://www.ssh.com/ssh/tunneling/example).

```
http://localhost:8000/status -> MGOB API /status
http://localhost:8000/version -> MGOB API /version
http://localhost:8000/storage -> MGOB API /storage # NOT TESTED
http://localhost:8000/metrics -> MGOB API /metrics to be used with Prometheus # NOT TESTED
http://localhost:8000/debug -> MGOB API /debug # NOT TESTED
http://localhost:8000/backup -> MGOB API /backup # NOT TESTED
```

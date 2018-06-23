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

_NOTE: docker-ce stable for Ubuntu 18.04 was released at the time of this writing, will be tested shortly._
Additional information can be found in tests/README.md.

## Role Variables
Every configuration variable available in [DemocracyOS Configuration](http://docs.democracyos.org/configuration.html) is available as an Ansible variable. While DemocracyOS Configuration uses `CONSTANT_CASE`, Ansible uses `democracyos_constant_case`, eg:
> `MONGO_URL` configuration variable is `democracyos_mongo_url` in Ansible.

**Docker Installation**

There's a special variable called `docker_install`, its default value is `true`, meaning that latest Docker CE stable version is installed in target host/s. Set it to `false` if you already have Docker i place.

**Tags**

This role has tasks grouped by tags, specifically:

* fresh\_install
* change\_version

#### Fresh Install Deployment
A fresh install means:

1. Install Docker CE (if `docker_install` is not set to `false`).
2. Check existence of and install docker-compose.
3. Download and run needed containers. By default democracyos 2.11.0 is installed, specific version can be installed by setting `democracyos_docker_image_version` variable (It's a string!).

Example:

```bash
$ sudo git clone -b refactor https://github.com/DemocracyOS/onpremises /etc/ansible/roles/onpremises

# Example playbook below:
# playbook.yml
- hosts: server1
  roles:
    role: onpremises

# End of playbook.yml

# Now we run our playbook
# --ask-become-pass prompts for sudoer password!
$ ansible-playbook playbook.yml --tags "fresh_install" --ask-become-pass
```

How is the deployment directory looking now?

```
/opt/democracy_os
-- docker-compose-infra.yml
-- docker-compose-app.2.11.0.yml
-- docker-volumes/
---- mongo_backup_storage/
---- mongo_backup_config/
---- mongo_backup_tmp/
---- mongo_backup_data/
---- mongo_container/
```

#### Change Version Deployment
Changing version means:

1. Downloading requested democracyos image, older or newer.
2. Run _new_ container and check that docker-compose return code is 0.
3. If everything _is fine_ stop _old_ container.

Example:

```bash
# This is our new playbook.yml
# playbook.yml
- hosts: server1
  roles:
    role: onpremises
      democracyos_docker_image_version: "2.11.7"

# End of playbook.yml

# Now we run our playbook
$ ansible-playbook playbook.yml --tags "change_version" --ask-become-pass
```

How is the deployment directory looking now?

```
/opt/democracy_os
-- docker-compose-infra.yml
-- docker-compose-app.2.11.0.yml
-- docker-compose-app.2.11.7.yml
-- docker-volumes/
---- mongo_backup_storage/
---- mongo_backup_config/
---- mongo_backup_tmp/
---- mongo_backup_data/
---- mongo_container/
```

## DemocracyOS + MGOB + Traefik
This is implemented but not completely tested. Running any test with vagrant will set next routes:

```
http://localhost:8080 -> Traefik dashboard
http://localhost:8000 -> DemocracyOS
http://localhost:8000/storage -> MGOB API /storage
http://localhost:8000/status -> MGOB API /status
http://localhost:8000/version -> MGOB API /version
```

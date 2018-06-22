# DemocracyOS On-Premises Ansible Role
Installation and deployment of DemocracyOS with Docker and Ansible.

## Description
This role is used to install and run:

* Docker with docker-compose.
* DemocracyOS docker-compose deployment.

## Requirements
* SSH access to your instance/s **must** be configured since we're using Ansible. It is strongly recommended to use certificate based access, a complete guide can be found [here](https://wiki.archlinux.org/index.php/SSH_keys). Also, using a SSH config file is a really good practice, guide [here](https://www.digitalocean.com/community/tutorials/how-to-configure-custom-connection-options-for-your-ssh-client).
* Ansible 2.1 or greater is required in administrator's machine, installation guide is [here](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).
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

## Example workflow

### 1) Clone Repo

```bash
$ sudo git clone -b refactor https://github.com/DemocracyOS/onpremises /etc/ansible/roles/onpremises
```

### 2) Create playbook

```
# Example playbook
- hosts: server1
  roles:
    - role: onpremises
```

### 3) Run playbook (--ask-become-pass is mandatory)

```bash
# become pass is your user sudo password
$ ansible-playbook playbook.yml --ask-become-pass
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

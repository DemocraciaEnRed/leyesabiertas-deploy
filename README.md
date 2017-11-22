# DemocracyOS On-premises

Given a Ansible Playbook, we are going to provision and start DemocracyOS on a CentOS/RedHat/Ubuntu VM.

## Requirements

* Make sure to have **Ansible 2.1.0.0** or greater installed locally ([Installation Guide](http://docs.ansible.com/ansible/intro_installation.html))
* Clone this repo and make sure to have the necessary [roles](http://docs.ansible.com/ansible/playbooks_roles.html) with:
  + `ansible-galaxy install -p ./roles -r ./requirements.yml`

## Provisioning

First, make sure to have a VM (where DemocracyOS is going to run) with `root` access to it, and can login with your SSH Key. If you don't know how to do any of that, follow [this](https://www.digitalocean.com/community/tutorials/how-to-connect-to-your-droplet-with-ssh) tutorial.

Then, you will have to customize how your environment is going to run. To do that you have to create an [Inventory file](http://docs.ansible.com/ansible/intro_inventory.html) and a configuration file with your custom variables. Use as reference [`inventories/example`](https://github.com/DemocracyOS/onpremises/blob/master/inventories/example) and [`inventories/example.yml`](https://github.com/DemocracyOS/onpremises/blob/master/inventories/example.yml). The file names should be the same as your environment, e.g.: `inventories/staging` and `inventories/staging.yml`.

Lastly, just run:

```
ansible-playbook playbook.yml -i inventories/<path to your inventory file>
```

This command is going to execute all the tasks defined at [playbook.yml](https://github.com/DemocracyOS/onpremises/blob/master/playbook.yml); if you need to know how that works, [here's](http://docs.ansible.com/ansible/playbooks.html) the official docs.

## How to manage your running VM

Inside your VM, you can run the following commands:

```
# Deploy a new Docker image
docker pull democracyos/democracyos:latest && docker-compose -f /usr/src/docker-compose.yml restart -d
```

```
# See the logs
docker-compose -f /usr/src/docker-compose.yml logs --follow
```

```
# View all the services running
docker-compose -f /usr/src/docker-compose.yml ps
```

```
# Connect to the running MongoDb console
docker exec -it src_mongo_1 mongo democracyos-staging
```

```
# Connect to the image running DemocracyOS
docker exec -it src_app_1 bash
```

```
# Scale the number of DemocracyOS instances running
docker-compose -f /usr/src/docker-compose.yml scale app=3
```

## MongoDB Backups

If you're running on `staging` or `production` environment, everyday at `23:59` a dump of the entire database will be saved to `/usr/src/backups`. You can check the file [`templates/backup.j2s`](https://github.com/DemocracyOS/onpremises/blob/master/templates/backup.j2) to see what it does.

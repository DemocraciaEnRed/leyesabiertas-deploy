## DemocracyOS On-premises

Given a Ansible Playbook, we are going to provision and start DemocracyOs App on a CentOS/RedHat/Ubuntu VM

## Requirements

- Vagrant 1.8.4 or greater (Only for testing locally)
- Ansible 2.1.0.0 or greater

## How to (Testing locally)

Just run `vagrant up`

## How it works (Testing locally)

- Vagrant will create the box with a custom IP: 192.168.13.37
- After the creation, the provisioning will begin:
	- First, Ansible will install the required roles
	- Then, it will run `playbook.yml` with the inventory file `vagrant` which is going to provision the VM and copy the composer file.
	- It will run create the composer cluster.

## How to (Staging)

First, you have to create the inventory file of the desired environment. [For more information](http://docs.ansible.com/ansible/intro_inventory.html) and set te required vars for the app (you can use the `vagrant` env file as a reference).

Run `ansible-playbook playbook.yml -i inventories/<name of your environment>`

## How to (Production)

First, you have to create the inventory file of the production environment. [For more information](http://docs.ansible.com/ansible/intro_inventory.html) and set te required vars for the app (you can use the `vagrant` env file as a reference).

Run `ansible-playbook playbook.yml -i inventories/production`
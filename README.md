## DemocracyOS On-premises

Given a Ansible Playbook, we are going to provision and start DemocracyOs App on a CentOS/RedHat VM

## Requirements

- Vagrant 1.8.4 or greater
- Ansible 2.1.0.0 or greater

## How to (Testing locally)

Just run `vagrant up`

## How it works (Testing locally)

- Vagrant will create the box with a custom IP: 192.168.13.37
- After the creation, the provisioning will begin:
	- First, Ansible will install the required role: Docker that will be used to install Docker / Docker Compose 
	- Then, it will run `playbook.yml` wich is going to provision the VM and copy the composer file
	- At last, it will run create the composer cluster

## How to (Production)

// To follow

## How it works (Production)

// To follow
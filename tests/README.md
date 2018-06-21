# Distribution Tests
Once this role is installed, deploy can be tested with included Vagrantfiles. In the future, testing with molecule might be included.

## Requirements

* Ubuntu 16.04 and newer need python-minimal to be manually installed, another workaround is to set ansible python interpreter to python3 in your inventory file: `ansible_python_interpreter=/usr/bin/python3`.

## Debian
Debian is supported from version 8 (Jessie) onwards given that version 7 (Wheezy) supports libc6 2.13 and >= 2.14 is needed for Docker Compose. We only include a test for Debian 8.10 but this was tested before for all Debian 8 minor releases, which include mostly just security upgrades (which should be mandatory for administrators).

> Links to Official Documentation:
> [Releases Documentation](https://wiki.debian.org/DebianJessie)
> [Vagrant images](https://app.vagrantup.com/debian/boxes/jessie64)

* Debian 8.10

## CentOS
CentOS is supported from version 7 given that older versions support only Python 2.6.6 (we need >= 2.7) and libc6 2.12 (we need >= 2.14). Official Vagrant boxes track CentOS releases with same timestamp starting from CentOS 7.3 so prior versions are not tested. Also, libselinux-python is installed by default on CentOS 7.

> Links to Official Documentation:
> [Releases Documentation](https://wiki.centos.org/Manuals/ReleaseNotes)
> [Base Distribution](https://wiki.centos.org/Download)
> [Vagrant images (CentOS 7)](https://app.vagrantup.com/centos/boxes/7)

* CentOS 7.3 (1611 -> Vagrant version: 1611.01)
* CentOS 7.4 (1708 -> Vagrant version: 1708.01)
* CentOS 7.5 (1804 -> Vagrant version: 1804.02)

## Ubuntu
Ubuntu is supported according to Docker CE support, starting from version 14.04 LTS. Vagrant images are constantly upgraded so these tests are based on latest version available.

> Links to Official Documentation
> [Releases](https://wiki.ubuntu.com/Releases)
> [Vagrant images (Ubuntu 14.04)](https://app.vagrantup.com/ubuntu/boxes/trusty64)
> [Vagrant images (Ubuntu 16.04)](https://app.vagrantup.com/ubuntu/boxes/xenial64)
> [Vagrant images (Ubuntu 17.10)](https://app.vagrantup.com/ubuntu/boxes/artful64)

**Bionic**
A stable docker release for Ubuntu 18.04 LTS (Bionic Beaver) is coming on late June 2018.

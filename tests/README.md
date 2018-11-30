# Pruebas de Distribuciones
Una vez que el rol está instalado, el despliegue puede ser probado con los Vagrantfiles aquí incluídos.

## Requerimientos

* Para Ubuntu 16.04 y nuevas versiones es necesario instalar manualmente python-minimal, una alternativa es asignar el interprete de python de Ansible a python3 en el archivo del inventario: `ansible_python_interpreter=/usr/bin/python3`.

## Debian

Debian está soportado desde la versión 8 (Jessie) en adelante ya que la versión 7 (Wheezy) soporta libc6 2.13 y >= 2.14 es necesaria para Docker Compose. Solo incluímos una prueba para Debian 8.10 pero fue testeada antes de los minor releases de Debian 8, que incluyen mas que nada actualizaciones de seguridad (que deberían ser obligatorias para administradores).

> Links a Documentación Oficial:
> [Documentación de Releases](https://wiki.debian.org/DebianJessie)
> [Imagenes Vagrant](https://app.vagrantup.com/debian/boxes/jessie64)

* Debian 8.10

## CentOS

CentOS es soportado desde la versión 7 ya que versiones anteriores solo soportan Python 2.6.6 (es necesaria >= 2.7) y libc6 2.12 (es necesaria >= 2.14). Las Vagrant boxes oficiales siguen los mismos timestamps de CentOS empezando por CentOS 7.3 así que versiones anteriores no fueron probadas. 
Además, libselinux-python está instalado por defecto en CentOS 7.

> Links a Documentación Oficial:
> [Documentación de Releases](https://wiki.centos.org/Manuals/ReleaseNotes)
> [Distribución](https://wiki.centos.org/Download)
> [Images Vagrant (CentOS 7)](https://app.vagrantup.com/centos/boxes/7)

* CentOS 7.3 (1611 -> Vagrant version: 1611.01)
* CentOS 7.4 (1708 -> Vagrant version: 1708.01)
* CentOS 7.5 (1804 -> Vagrant version: 1804.02)

## Ubuntu

Ubuntu es soportado de acuedo al soporte de Docker CE, empezando por la versión 14.04 LTS. Las imagens Vagrant son actualizadas constantemente por eso estas pruebas están basadas en las últimas versiones disponibles.

> Links a Documentación Oficial
> [Releases](https://wiki.ubuntu.com/Releases)
> [Vagrant images (Ubuntu 14.04)](https://app.vagrantup.com/ubuntu/boxes/trusty64)
> [Vagrant images (Ubuntu 16.04)](https://app.vagrantup.com/ubuntu/boxes/xenial64)
> [Vagrant images (Ubuntu 17.10)](https://app.vagrantup.com/ubuntu/boxes/artful64)

## Cómo probar

Las pruebas necesitan que el rol esté instalado y accesible a Ansible, esto se detalla en el Readme principal. Es necesario un Vagrantfile y su playbook. Pruebas con HTTPS requieren certificados y llaves válidas o auto firmadas.

```bash
$ mv Vagrantfile-https Vagrantfile
$ vagrant up

# Navega a https://localhost:8443/ para usar Leyes Abiertas
```
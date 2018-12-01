# Deploy en Azure

Es posible probar este rol en Azure, creando una máquina virtual. Nuevamente se utiliza Ansible para el deploy. 

ATENCIÓN: el usuario debe contar con una subscripción en Azure. La utilización de esta guía puede (y debería) implicar costos por la utilización de recursos en la nube de Microsoft. 

# Requerimientos

## Ansible 2.5.5
Como se muestra en el README principal, lo primero es contar con Ansible instalado, en este caso, debido a los módulos necesarios, la versión mínima a instalar es la 2.5.5:

**Verificar la versión de Ansible**

```bash
# Primero es necesario verificar la versión actual
$ ansible --version
# Si es mayor o igual a 2.5.5 entonces no es necesario realizar cambios.
```

**Desinstalar versiones anteriores de Ansible**

(Solo si la versión actual es anterior a 2.5.5)

```bash
# Si ansible fue instalado mediante pip:
$ sudo pip uninstall ansible

# Si ansible fue instalado mediante apt en Debian/Ubuntu:
$ sudo apt-get remove --purge ansible

# Si ansible fue instalado mediante yum en RHEL/CentOS:
$ sudo yum remove ansible

# Si ansible fue instalado mediante dnf en Fedora:
$ sudo dnf remove ansible
```

**Instalar Ansible 2.5.5**

Para instalar versiones específicas de Ansible es necesario hacerlo con el administrador de dependencias de Python: pip.

```bash
# Instalar pip en Debian/Ubuntu:
$ sudo apt-get install python-pip

# Instalar pip en RHEL/CentOS:
$ sudo yum install python2-pip

# Instalar pip en Fedora:
$ sudo dnf install python2-pip
```

Ahora si es posible instalar Ansible:

```bash
$ sudo pip install ansible==2.5.5
```

## Azure CLI

El segundo requerimiento es contar la consola de comandos de Azure, para ello ya existe una guía oficial completa [aquí](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

Una vez instalada la CLI de Azure solo resta instalar una dependencia más para Ansible:

```bash
# Al utilizar bash:
$ sudo pip install ansible[azure]

# Al utilizar ZSH:
$ sudo pip install 'ansible[azure]'
```

## Correr los playbooks
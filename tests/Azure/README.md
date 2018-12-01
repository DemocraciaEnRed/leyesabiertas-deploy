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

**Instalar pip**

Para instalar versiones específicas de Ansible es necesario hacerlo con el administrador de dependencias de Python: pip.

```bash
# Instalar pip en Debian/Ubuntu:
$ sudo apt-get install python-pip

# Instalar pip en RHEL/CentOS:
$ sudo yum install python2-pip

# Instalar pip en Fedora:
$ sudo dnf install python2-pip
```

**Instalar Ansible**

Ahora si es posible instalar Ansible:

```bash
$ sudo pip install ansible==2.5.5
```

## Azure CLI

El segundo requerimiento es contar la consola de comandos de Azure, para ello ya existe una guía oficial completa [aquí](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

IMPORTANTE: es necesario completar la instalación y realizar exitosamente el login mediante `$ az login`.

Una vez instalada la CLI de Azure solo resta instalar una dependencia más para Ansible:

```bash
# Al utilizar bash:
$ sudo pip install ansible[azure]

# Al utilizar ZSH:
$ sudo pip install 'ansible[azure]'
```

# Playbooks

Para aislar cada trabajo en su playbook, el deploy se divide en dos archivos.

**Creación de la VM en Azure**

El primer playbook es `crear-vm.yml`, utilizando los módulos de Ansible para Azure se crean:

* Un Resource Group: una entidad lógica que agrupa recursos relacionados. En este caso su nombre es `azure-test-rg`.
* Una Virtual Network: una red virtual donde va a estar conectada la VM. En este caso su nombre es `azure-test-net`.
* Un Security Group: son reglas de firewall que regulan el acceso a recursos dentro de una virtual network. En este caso su nombre es `azure-test-sg`. En el playbook se permite el acceso desde Internet a los puertos 22, 80 y 443 para acceder tanto a la VM como a su servicio web. Se permite la salida a los puertos 80 y 443 para la descarga de dependencias y se niega el resto del tráfico.
* Una Subnet: es necesario definir una subred dentro de la virtual network principal, la explicación de esta configuración sobrepasa los objetivos de esta guía. En este caso el nombre de la subnet es `azure-test-sn`.
* Una IP pública dinámica: para poder acceder a la VM desde internet es necesaria una dirección IPv4 pública, en este caso es una dirección dinámica y puede cambiar en el tiempo. El nombre de este recurso en este caso es `azure-test-pip`.
* Una interfaz de red: se crea una interfaz de red que agrupa los recursos creados hasta el momento: una virtual network, una subnet, un security group y una IP pública. Este recurso es independiente, en el próximo paso es asociado a la VM que se va a utilizar. El nombre de este recurso es `azure-test-nic`.
* VM: finalmente el último recurso es la VM, en este ejemplo se utiliza una CentOS 7 en una instancia `Standard_B2s` con dos núcleos y 4GB de RAM que son valores recomendados. Es necesario rellenar el campo `key_data` con la llave pública del usuario para garantizar el acceso por SSH.

**Hostname de la VM**

Este playbook permite establecer un nombre de host para la VM que luego puede utilizarse para acceder remotamente sin la necesidad de conocer la dirección IPv4 pública.

Es necesario cambiar el valor de `domain_name` en la tarea `Crear IP publica dinamica`:

```yaml
- name: Crear IP publica dinamica
      azure_rm_publicipaddress:
        resource_group: azure-test-rg
        name: azure-test-pip
        allocation_method: Dynamic
        domain_name: <NOMBRE-HOST>
      register: public_ip
```

Luego, es necesario conocer el nombre de la Availabilty Zone donde está alojada la VM. Esto también puede configurarse en el playbook estableciendo el valor de `location` en la task `Crear Resource Group`:

```yaml
- name: Crear Resource Group
      azure_rm_resourcegroup:
        name: azure-test-rg
        location: westus2
        tags:
          stage: <NOMBRE-AVAILABILITY-ZONE>
          type: vm
```

Una vez finalizada la creación de la VM, su nombre de host en los servidores DNS de Microsoft será:

`<NOMBRE-HOST>.<NOMBRE-AVAILABILITY-ZONE>.cloudapp.azure.com`

Siguiendo el ejemplo del playbook, este será:

`azure-test.westus2.cloudapp.azure.com`.

**Correr el playbook**

Una vez entendidos todos estos pasos, es posible crear la VM:

```bash
$ ansible-playbook crear-vm.yml
```

El mismo playbook informa, una vez creada la VM, tanto la dirección IPv4 pública como el nombre de host.

Esto también puede verificarse mediante la CLI de Azure:

```bash
# Listar las VMs en Azure
$ az vm list --output table
Name              ResourceGroup      Location    Zones
----------------  -----------------  ----------  -------
azure-test        AZURE-TEST-RG      westus2

# Listar las direcciones IPv4 públicas en Azure
$ az vm list-ip-addresses --output table
VirtualMachine    PublicIPAddresses    PrivateIPAddresses
----------------  -------------------  --------------------
azure-test        X.X.X.X              Y.Y.Y.Y
```

**Destruir lo creado**

En caso de querer borrar todo lo creado en esta guía, es posible mediante la CLI de Azure:

```bash
$ az resource delete --resource-group azure-test-rg
```

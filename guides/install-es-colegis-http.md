# Guías de Instalación

## Sistema de Co-Creación Legislativa (Versión HTTP)
Instalación del Sistema de Co-Creación Legislativa en un servidor GNU/Linux.

**Resumen**

En esta guía vamos a trabajar desde la perspectiva del administrador, todos los pasos son válidos para cualquier sistema operativo basado en GNU/Linux y se ejecutan desde la línea de comandos de una terminal.

**Requerimientos**

El administrador debe contar con:

* Una computadora con un sistema operativo basado en GNU/Linux o MacOS. Es recomendable utilizar alguna de las distribuciones más conocidas como [Debian](https://www.debian.org), [Fedora](https://getfedora.org) o [Ubuntu](https://www.ubuntu.com). Debe contar con un usuario con permisos de [sudo](https://wiki.archlinux.org/index.php/Sudo_(Español)) en el sistema.

* Un servidor o máquina virtual con un sistema operativo basado en GNU/Linux. Es recomendable utilizar distribuciones conocidas como [CentOS 7](https://www.centos.org) o [Ubuntu Server](https://www.ubuntu.com/server) (versión 14.04 en adelante). Debe contar con un usuario con permisos de [sudo](https://wiki.archlinux.org/index.php/Sudo_(Español)) en el sistema.

* Conocimientos mínimos de administración de sistemas operativos basados en GNU/Linux, en especial la operación de la línea de comandos de la terminal, configuración de accesos SSH mediante llaves asimétricas y edición de ficheros con editores como [GNU nano](https://es.wikipedia.org/wiki/GNU_Nano) o [vim](https://es.wikipedia.org/wiki/Vim).

**Antes de comenzar**
Se mostrarán los comandos a ejecutar en bloques con el siguiente formato:

```bash
# Esta es una terminal
$ <comandos>
<Output o salida del comando> 
```

La salida de estos comandos puede diferir de un sistema a otro.
A modo de ejemplo, vamos a asumir los siguientes datos:

* Usuario local: `admin`.
* Usuario en el servidor: `user`.
* IPv4 del servidor: `192.168.0.10`


---

### 0) Instalación de Python y pip

La primera dependencia para este proyecto es [Python](https://www.python.org/). Python se encuentra incluído por defecto en la mayoría de las distribuciones de Linux. En esta guía trabajamos con Python en versiones 2.7.X

```bash
# Comprobar la instalación de Python
$ python --version
Python 2.7.15
```

[Pip](https://es.wikipedia.org/wiki/Pip_(administrador_de_paquetes)) es un administrador de paquetes hechos en Python. Por defecto no suele estar instalado en las distribuciones de Linux por lo tanto es necesaria su instalación.

```bash
# Instalación en Debian
$ sudo apt-get install python-pip

# Instalación en Fedora 27/28
$ sudo dnf install python2-pip

# Instalación en Ubuntu
$ sudo apt-get install python-pip
```

### 1) Instalación de Ansible

[Ansible](https://www.ansible.com) es una herramienta de automatización de tareas y es el núcleo de la instalación de este sistema. Por lo general puede instalarse desde los repositorios de las distintas distribuciones pero en este caso vamos a instalarlo con pip para lograr más flexibilidad en la versión a elegir.

```bash
# Instalación de Ansible
$ sudo pip install ansible

# Comprobar la instalación de Ansible
$ ansible --version
```

### 2) Configuración de acceso por SSH al servidor

Como fue mencionado anteriormente es necesario acceder sin especificar password al servidor. Esto puede lograrse configurando un par de llaves asimétricas. Una llave, la privada, se coloca en la computadora del administrador; mientras que la otra, la pública, se coloca en el directorio home del usuario en el servidor.

**2.1) Par de llaves asimétricas**

Se utiliza el comando `ssh-keygen` para generar el par de llaves, el mismo le pide al usuario una passphrase para encriptar las llaves, esto no es necesario por el momento. Cuando el programa pida esta passphrase solo es necesario presionar `Enter`.

```bash
# Generación de un par de llaves RSA, se generan dos ficheros y su ubicación está dada por el flag -f
$ ssh-keygen -t rsa -b 4096 -f ~/.ssh/onpremises-test -C "admin@local-computer"
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/alvaro/.ssh/onpremises-test.
Your public key has been saved in /Users/alvaro/.ssh/onpremises-test.pub.
The key fingerprint is:
SHA256:l9s0ActaoMuKBh8HVip23M7WAUnqHLbpJ01LAAzXDjM admin@local-computer
The key's randomart image is:
+---[RSA 4096]----+
|+...oo. . .      |
| oEo+... o o     |
|..+% ...  + .    |
|.o=.O....o . .   |
|. .=.*o.S o o    |
| o.+=..  . + .   |
|  +o.+    . .    |
| .  o            |
|                 |
+----[SHA256]-----+

# Comprobar que las llaves fueron generadas
$ ls -l ~/.ssh
-rw-r--r--  1 admin  admin  1305 Sep 28 00:05 config
-rw-------  1 admin  admin  3389 Nov 13 00:13 onpremises-test
-rw-r--r--  1 admin  admin   746 Nov 13 00:13 onpremises-test.pub
```

Ahora es necesario copiar la llave pública (aquella con extensión *.pub) en el fichero `/home/user/.ssh/authorized_keys`. Si el directorio no existe debe ser creado, lo mismo para el fichero `authorized_keys`.

```bash
# Mostrar la llave pública
$ cat ~/.ssh/onpremises-test.pub
ssh-rsa <contenido de la llave>...

# Una vez copiada, acceder al servidor
$ ssh user@192.168.0.10

# Verificar si existe ~/.ssh
user@servidor $: cd && ls -la | grep .ssh

# Si no existe crear ~/.ssh
user@servidor $: mkdir .ssh && chmod 700 .ssh

# Una vez que contamos con .ssh, crear o editar el fichero authorized_keys
user@servidor $: touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys

# Pegar la llave pública
user@servidor $: echo "ssh-rsa <contenido de la llave>..." >> ~/.ssh/authorized_keys

# Salir del servidor
user@servidor $: exit
```

**2.2 Configuración del servidor en SSH config local**

Siguiendo el ejemplo del servidor, es necesario agregar el siguiente contenido en `~/.ssh/config` para poder configurar el acceso automático al servidor. Agregar al final de dicho fichero lo siguiente:

```bash
Host onpremises-test
  HostName 192.168.0.10
  Port 22
  User user
  IdentityFile ~/.ssh/onpremises-test
```

Lo único relevante de la configuración anterior es que desde SSH nos vamos a referir al servidor con el nombre `onpremises-test` en lugar de su hostname o dirección IPv4. Este nombre servirá más adelante como referencia para Ansible.

Una vez finalizados estos pasos, será posible acceder por SSH sin password al servidor y además solo especificando el nombre anteriormente mencionado.

```bash
# Acceder al servidor sin password y utilizando su nombre de la configuración
$ ssh onpremises-test
user@servidor $: 
```

### 3) Inventario de Ansible

Ansible se utiliza para automatizar tareas, por lo tanto su uso más común es realizar trabajos en servidores. Para que Ansible conozca servidores, deben ser especificados en su [inventario](https://docs.ansible.com/ansible/2.3/intro_inventory.html). El mismo es un fichero y se encuentra en `/etc/ansible/hosts`. Su formato básico es el siguiente:

```bash
[nombre-de-grupo]
nombre-de-servidor  ansible_host=example.com    ansible_port=22 ansible_user=user   ansible_connection=ssh
```

Los parámetros son:
* _nombre-de-grupo_: los servidores suelen agruparse bajo un mismo nombre, en este caso, esto no tiene relevancia.

* _ansible_host_: dirección IPv4, FQDN o nombre en la configuración SSH del servidor a utilizar.

* _ansible_port_: puerto utilizado por Ansible para la conexión con el servidor.

* _ansible_user_: usuario utilizado por Ansible para la conexión con el servidor.

* _ansible_connection_: tipo de conexión que utilizará Ansible, no relevante por el momento.

Siguiendo con el ejemplo aquí mostrado, es necesario editar/crear el fichero `/etc/ansible/hosts` como superusuario:

```bash
# Editar /etc/ansible/hosts
$ sudo nano /etc/ansible/hosts

# Agregar al fichero el siguiente contenido
[onpremises]
test1    ansible_host=onpremises-test    ansible_port=22     ansible_user=user   ansible_connection=ssh
```

Con todo esto en su lugar, ya podemos probar Ansible sobre el servidor:

```bash
# Prueba de Ansible mediante módulo ping
# El formato del comando es: $ ansible <nombre-del-host-en-el-inventario> -m <módulo>
$ ansible test1 -m ping
test1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

El módulo ping se conecta por SSH al servidor y si todo sale bien devuelve un "pong" como el del ejemplo.

### 4) Instalación de onpremises como rol de Ansible

El repositorio onpremises no es más que un rol de Ansible. Un rol agrupa tareas, plantillas, variables y toda la información necesaria para lograr un objetivo. Los roles deben ser instalados en `/etc/ansible/roles`. En este caso puede clonarse el repositorio directamente.

```bash
# Crear el directorio de roles si no existe
$ sudo mkdir /etc/ansible/roles

# Clonar el repositorio onpremises en el destino indicado.
# NOTA: en este momento estamos trabajando sobre el branch integracion-productos
$ sudo git clone -b integracion-productos https://github.com/DemocracyOS/onpremises /etc/ansible/onpremises
```

### 5) Elaboración del playbook de instalación

La aplicación de tareas y roles de Ansible se realiza mediante la definición de un playbook en formato [yaml](http://yaml.org/). Los roles suelen definir un juego de variables que ayudan a ajustar las tareas automatizadas. Es decir, en esta etapa deben concentrarse los esfuerzos en estudiar las variables en juego y cómo afectan la instalación del sistema. Todas las variables intervinientes pueden encontrarse en el README principal de este repositorio. Muchas de ellas son opcionales o toman valores por defecto que no afectan el normal funcionamiento. Para este ejemplo, vamos a utilizar las siguientes variables:

* `democracyos_docker_image`: es la imagen de Docker de la aplicación principal del sistema. En este ejemplo su valor es `democraciaenred/congreso:development`. Obligatoria.

* `democracyos_core_image`: es la imagen de Docker con la API de la aplicación. En este ejemplo su valor es `democracyos/core:development`. Obligatoria.

* `democracyos_keycloak_image`: es la imagen de Docker con la aplicación Keycloak. En este ejemplo su valor es `democracyos/keycloak:latest`. Obligatoria.

* `democracyos_notifier_image`: es la imagen de Docker con la aplicación para notificaciones. En este ejemplo su valor es `democracyos/notifier:development`. Obligatoria.

* `democracyos_protocol`: protocolo que utilizará la aplicación para funcionar, puede tomar uno de dos valores, `http` o `https`. En este ejemplo su valor es `http`. No obligatoria, por defecto su valor es `http`.

* `democracyos_host`: nombre de host que va a reconocer el sistema para ser utilizado. En este ejemplo su valor es `192.168.0.10` ya que esto es una prueba. No obligatoria, por defecto su valor es `localhost`.

* `keycloak_realm`: nombre de Realm a utilizar en Keycloak. En este ejemplo su valor es `congreso`. Obligatoria. Su valor por defecto es `DemocracyOS`. 

Una vez definidas todas estas variables, se puede conformar un `playbook.yml`:

```yaml
# playbook.yml
- hosts: test1
  roles:
    - role: onpremises
      democracyos_docker_image: "democraciaenred/congreso:development"
      democracyos_core_image: "democracyos/core:development"
      democracyos_keycloak_image: "democracyos/keycloak:latest"
      democracyos_notifier_image: "democracyos/notifier:development"
      democracyos_protocol: http
      democracyos_host: 192.168.0.10
      keycloak_realm: DemocracyOS
```

### 6) Ejecución del playbook

Una vez que están definidas todas las piezas, es posible ejecutar el playbook. Tal como está descrito en el README principal, este rol de Ansible separa tareas mediante la imposición de etiquetas. Para una primera instalación esta etiqueta es `fresh_install`, que permite ejecutar solo las tareas necesarias para instalar el sistema desde cero. La otra etiqueta disponible es `change_version` que permite actualizar componentes de la instalación, su uso se demostrará más adelante.

Tal como se menciona en el README principal, la ejecución del playbook pide al usuario que ingrese la contraseña de sudo del usuario del servidor ya que ciertas tareas requieren permisos de superusuario. Por lo tanto el administrador debe conocer e ingresar dicha contraseña.

```bash
# Ejecución del rol de Ansible mediante un playbook
# El formato del comando es: $ ansible-playbook <fichero-del-playbook> [opciones]
$ ansible-playbook playbook.yml --tags "fresh_install" --ask-become-pass
```

La ejecución de las tareas depende en gran medida de las capacidades del servidor, tanto en memoria como frecuencia de CPU. Un factor externo es el ancho de banda.

Una vez terminadas las tareas y quizás después de por lo menos un minuto, la aplicación se encontrará disponible en `http://192.168.0.10`.

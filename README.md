# Rol de Ansible para Leyes Abiertas
Instalación y despliegue de Leyes Abiertas con Docker y Ansible.

## Descripción
Leyes abiertas es una aplicación web basada en múltiples servicios que corren en Docker. Este rol está basado en [un proyecto usado para DemocracyOS](https://github.com/DemocracyOS/onpremises) y sirve para instalar y ejecutar:

* Docker junto a Docker Compose.
* Los servicios de Leyes Abiertas.

**IMPORTANTE**: este rol contiene tareas agrupadas por tags, leer la sección de tags debajo.

## Requerimientos
* Configurar el acceso SSH a la/s instancia/s para el uso de Ansible. Se recomienda usar certificados en el acceso, una guía completa puede encontrarse [aquí](https://wiki.archlinux.org/index.php/SSH_keys). Otra buena práctica usar un archivo de configuración una guia puede encontrarse [aquí](https://www.digitalocean.com/community/tutorials/how-to-configure-custom-connection-options-for-your-ssh-client).
* Tener instalado en la maquina del administrador Ansible 2.2, una guía completa de instalación puede encontrarse [aquí](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).
* Clonar éste repositorio en el directorio de roles de Ansible (`roles_path`), que suele ser `/etc/ansible/roles` en Linux o `~/.ansible/roles` en MacOS.
* Configurar el [Inventorio de Ansible](https://docs.ansible.com/ansible/2.3/intro_inventory.html) con la/s instancia/s administradas donde Leyes Abiertas será deplegada. Ejemplo:

```
[production]
server1    ansible_ssh_host=server1.ejemplo.com    ansible_connection=ssh    ansible_port=22    ansible_user=admin
```
**IMPORTANTE**: para Ubuntu 16.04 y nuevas versiones es necesario tener instalad el paquete `python-minimal`, ya que desde python 2 no viene instalado por defecto.

## Plataformas soportadas
El despliegue está basado en [Docker](https://www.docker.com/) y [Docker Compose](https://docs.docker.com/compose/), por eso solo son soportadas plataformas que cumplan los requerimientos para usar Docker. Notese que **la instalación de Docker es opcional** asignando `docker_install` a `false`, por eso éste rol puede ser usado con sistemas existentes. Este rol fue testeado con Vagrant usando solo boxes oficiales:

* CentOS 7 (7.3, 7.4 and 7.5).
* Debian 8.
* Ubuntu 14.04, 16.04, 17.10.

_NOTA: docker-ce stable para Ubuntu 18.04 fue lanzada al momento de escribir este documento y será testeado pronto._

Información adicional al respecto podrá ser hayada en tests/README.md.

## Imagenes de Servicios Docker

Este rol instala los siguientes servicios creados o implementados por [DemocraciaEnRed](https://github.com/DemocraciaEnRed) como Imagenes Docker:

* **Leyes Abiertas Aplicación Web**: a list of versions can be found [here](https://hub.docker.com/r/democraciaenred/leysabiertas-web/tags/).
* **Leyes Abiertas Core API**: a list of versions can be found [here](https://hub.docker.com/r/democraciaenred/leysabiertas-core/tags/).
* **Leyes Abiertas Keycloak**: a list of versions can be found [here](https://hub.docker.com/r/democraciaenred/leysabiertas-keycloak/tags/).
* **Leyes Abiertas Notifier**: a list of versions can be found [here](https://hub.docker.com/r/democraciaenred/leysabiertas-notifier/tags/).

**IMPORTANTE**: Los servicios no tienen una versión por defecto ya que pueden ser actualizadas usando el rol en cualquier momento.

## Variables del Rol
Variables disponibles y sus valores por defecto:

_Docker_

| Variable                           | Valor por defecto                                 | Descripción                              |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `docker_compose_version`           | `1.21.2`                                          | Elegir versión de docker-compose.        |
| `docker_install`                   | `true`                                            | Instalar docker.                         |

_Leyes Abiertas general_

| Variable                           | Valor por defecto                                 | Descripción                              |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `leyesabiertas_web_image`          | `undefined`                                       | Imagen de Leyes Abiertas Aplicación Web. |
| `leyesabiertas_core_image`         | `undefined`                                       | Imagen de Leyes Abiertas Core API.       |
| `leyesabiertas_keycloak_image`     | `undefined`                                       | Imagen de Leyes Abiertas Keycloak.       |
| `leyesabiertas_notifier_image`     | `undefined`                                       | Imagen de Leyes Abiertas Notifier.       |
| `leyesabiertas_database_name`      | `leyeasabiertas`                                  | Nombre de base de datos para MongoDB.    |
| `leyesabiertas_protocol`           | `http`                                            | Protocolo a ser usado en las URL.        |
| `leyesabiertas_host`               | `localhost`                                       | Nombre de dominio del host.              |
| `leyesabiertas_api_host`           | Valor de `leyesabiertas_host`                     | Host de servicio Leyes Abiertas Core API.|
| `leyesabiertas_keycloak_host`      | Valor de `leyesabiertas_host`                     | Host de servicio Leyes Abiertas Keycloak.|
| `leyesabiertas_jwt_secret`         | `undefined`                                       | Clave de cifrado de sesiones de Core API.|


_Keycloak_

| Variable                           | Valor por defecto                                 | Descripción                              |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `keycloak_user`                    | `keycloak`                                        | Nombre de usuario Admin.                 |
| `keycloak_password`                | `keycloak`                                        | Contraseña de usuario Admin.             |
| `keycloak_realm_file`              | `undefined`                                       | Archivo para inicializar el Realm.       |
| `keycloak_realm`                   | `leyesabiertas`                                   | Nombre del Realm.                        |
| `keycloak_realm_desc`              | `Leyes Abiertas`                                  | Descripción del Realm.                   |
| `keycloak_theme`                   | `democracyos`                                     | DemocracyOS Keycloak Theme.              |
| `keycloak_smtp_host`               | `undefined`                                       | SMTP host.                               |
| `keycloak_smtp_user`               | `undefined`                                       | SMTP user.                               |
| `keycloak_smtp_password`           | `undefined`                                       | SMTP password.                           |
| `keycloak_from_account`            | `undefined`                                       | Correo del remitente.                    |
| `keycloak_replyto_account`         | `undefined`                                       | Correo de respuesta.                     |
| `keycloak_replyto_name`            | `undefined`                                       | Nombre del correo de respuesta.          |

**IMPORTANTE**: Al importar un realm con un archivo JSON el nombre del realm usado en el archivo debe coincidir con el asignado en `keycloak_realm`. 
**IMPORTANTE**: Si `keycloak_smtp_host` fue definida, entonces el resto de las variables del mail son obligatorias.

_Notifier_

| Variable                           | Valor por defecto                                 | Descripción                              |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `notifier_organization_email`      | `email@example.com`                               | Correo del remitente.                    |
| `notifier_organization_name`       | `Example`                                         | Nombre de la organización.               |
| `notifier_nodemailer_host`         | `smpt.example.com`                                | SMTP host.                               |
| `notifier_nodemailer_user`         | `user`                                            | SMTP user.                               |
| `notifier_nodemailer_pass`         | `password`                                        | SMTP password.                           |

_Directorios del despliegue_

| Variable                           | Valor por defecto                                 | Descripción                              |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `install_dir_path`                 | `/opt/leyes_abiertas`                             | Ruta del despliegue.                     |
| `docker_volumes_path`              | `{{ install_dir_path}}/docker_volumes`            | Ruta de volumenes de Docker.             |
| `traefik_config_dir_path`          | `{{ docker_volumes_path }}/traefik`               | Ruta de volmen de Traefik.               |

_Configuración HTTPS_

| Variable                           | Valor por defecto                                 | Descripción                              |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `enable_own_certificate`           | `false`                                           | Usar certificado y llave propia.         |
| `https_certificate_path`           | `undefined`                                       | Ruta absoluta al certificado.            |
| `https_key_path`                   | `undefined`                                       | Ruta absoluta a la llave.                |

_Let's Encrypt_

| Variable                           | Valor por defecto                                 | Descripción                              |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `enable_lets_encrypt`              | `false`                                           | Usar Let's Encrypt.                      |
| `enable_lets_encrypt_staging`      | `true`                                            | Usar Let's Encrypt Staging CA Server.    |
| `lets_encrypt_email`               | `undefined`                                       | Email de dominio para Let's Encrypt.     |

_MySQL (Keycloak Database)_

| Variable                           | Valor por defecto                                 | Descripción                              |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `enable_external_mysql`            | `false`                                           | Usar MySQL Server externo.               |
| `mysql_host`                       | `mysql`                                           | MySQL Server externo host.               |
| `mysql_port`                       | `3306`                                            | MySQL Server externo puerto.             |

_MongoDB_

| Variable                           | Valor por defecto                                 | Descripción                              |
|------------------------------------|---------------------------------------------------|------------------------------------------|
| `enable_external_mongo`            | `false`                                           | Usar MongoDB Server externo.             |
| `mongo_host`                       | `mongo`                                           | MongoDB Server externo host.             |
| `mongo_port`                       | `27017`                                           | MongoDB Server externo puerto.           |
| `mongodb_backup_storage_dir_path`  | `{{ docker_volumes_path }}/mongo_backup_storage`  | Volumen de almacenamiento de MGOB.       |
| `mongodb_backup_config_dir_path`   | `{{ docker_volumes_path }}/mongo_backup_config`   | Volumen de configuración de MGOB.        |
| `mongodb_backup_tmp_dir_path`      | `{{ docker_volumes_path }}/mongo_backup_tmp`      | Volumen /tmp MGOB.                       |
| `mongodb_backup_data_dir_path`     | `{{ docker_volumes_path }}/mongo_backup_data`     | Volumen de data de MGOB.                 |
| `mongodb_volume_dir_path`          | `{{ docker_volumes_path }}/mongo_container`       | Volumen del container MongoDB.           |
| `enable_mgob`                      | `false`                                           | Instalar MGOB para backups de MongoDB.   |


## Servicios externos
Es posible usar servicios externos para MySQL y MongoDB. En el caso de ser usados las siguientes variables deberán ser definidas:

* `enable_external_mongo`: `true`
* `mongo_host`: MongoDB Server externo host or IPv4 address.
* `mongo_port`: MongoDB Server externo puerto (defaults to 27017)

For MySQL:

* `enable_external_mysql`: `true`
* `mysql_host`: MySQL Server externo host or IPv4 address.
* `mysql_port`: MySQL Server externo puerto (defaults to 3306).
* `mysql_user`: Un usuario **debe** existir en el server.
* `mysql_password`: El usuario **debe** tener una contraseña configurada.
* `mysql_database`: Una base de datos **debe** existir y `mysql_user` **debe** tener todos los privilegios asignados.

## Instalación de Docker

La variable `docker_install` al ser asignada al valor `true`, provoca que sea instalada la última versión estable de Docker CE en la/s instancia/s. Asignar el valor `false` si ya existe una instalación de Docker.

## Tags

Las tareas del rol estan agrupadas en tags, específicamente:

* fresh\_install
* change\_version

#### Despliegue desde cero (Fresh Install)
El despliegue desde cero realiza los siguientes pasos:

1. Se instalará Docker CE (si `docker_install` no es igual a `false`).
2. Instalar Docker Compose si no está instalado.
3. Descargar y ejecutar los containers necesarios.

Ejemplo:

```bash
$ sudo git clone https://github.com/DemocraciaEnRed/leyesabiertas-deploy /etc/ansible/roles/onpremises

# Ejemplo de Playbook:
# playbook.yml
- hosts: server1
  roles:
    role: onpremises
      leyesabiertas_web_image: "democraciaenred/leyesabiertas-web:1.0.0"
      leyesabiertas_core_image: "democraciaenred/leyesabiertas-core:1.0.0"
      leyesabiertas_keycloak_image: "democraciaenred/leyesabiertas-keycloak:1.0.0"
      leyesabiertas_notifier_image: "democraciaenred/leyesabiertas-notifier:1.0.0"
      leyesabiertas_host: www.ejemplo.com

# Fin de playbook.yml

# Ya podemos ejecutar nuestra playbook
# --ask-become-pass pide la contraseña sudo de la/s instancia/s!
$ ansible-playbook playbook.yml --tags "fresh_install" --ask-become-pass
```

¿Cómo se ven ahora los directorios del despliegue?

```
/opt/leyes_abiertas
-- docker-compose.yml
-- docker-volumes/
---- mongo_backup_storage/
---- mongo_backup_config/
---- mongo_backup_tmp/
---- mongo_backup_data/
---- mongo_container/
---- traefik/
```

#### Cambiar versiones del despliegue (Change Version)
Cambiar versiones realiza los siguientes pasos:

1. Descargar las imagenes especificadas.
2. Ejecutar los _nuevo_ containers y esperar que docker-compose devuelva el código 0.
3. Si todo _sale bien_ limpiar _viejos_ containers.

**IMPORTANTE**: Cambiar versiónes implica actualizar la playbook existente, no crear una nueva!

Ejemplo:

```bash
# Esta es nuestra playbook.yml
# Mantén las variables viejas!
# playbook.yml
- hosts: server1
  roles:
    role: onpremises
      # leyesabiertas_web_image: "democraciaenred/leyesabiertas-web:1.0.0"
      leyesabiertas_web_image: "democraciaenred/leyesabiertas-web:1.0.1"
      leyesabiertas_core_image: "democraciaenred/leyesabiertas-core:1.0.0"
      leyesabiertas_keycloak_image: "democraciaenred/leyesabiertas-keycloak:1.0.0"
      leyesabiertas_notifier_image: "democraciaenred/leyesabiertas-notifier:1.0.0"
      leyesabiertas_host: www.ejemplo.com

# Fin de playbook.yml

# Ejecutamos nuestra playbook
$ ansible-playbook playbook.yml --tags "change_version" --ask-become-pass
```

¿Cómo se ven ahora los directorios del despliegue?

```
/opt/leyes_abiertas
-- docker-compose-app.SOME-TIMESTAMP.yml
-- docker-compose-app.yml
-- docker-volumes/
---- mongo_backup_storage/
---- mongo_backup_config/
---- mongo_backup_tmp/
---- mongo_backup_data/
---- mongo_container/
---- traefik/
```

## HTTP/HTTPS routing y load-balancing con Traefik. MGOB API.

### Traefik

[Traefik](https://traefik.io/) es un reverse proxy / load balancer implementado en [Go](https://golang.org/) que permite varias de las funcionalidades de éste despliegue. Este rol rol usa una combinación de configuraciones estáticas y dinámicas. Entrypoints, certificados y llave HTTPS (si se usa) y Let's Encrypt (si se usa) son parte de la configuración estática con el modulo de templates de Ansible. Frontends y backends son parte de la configuración dinámica con Docker y labels de containers. Para entender mejor la terminología, referirse a [la documentación de Traefik](https://docs.traefik.io).

Servicios disponibles con Traefik:

* HTTP Entrypoints (Disponibles por defecto):
  * Traefik escucha en 127.0.0.1:8080 para permitir acceder a su dashboard que no debe ser públicamente expuesto.
  * Traefik escucha en 127.0.0.1:8000 y dirige las llamadas a MGOB API (ver abajo), esto tampoco debe ser públicamente expuesto.
  * Traefik escucha en 0.0.0.0:80 y redirige las llamadas al HTTPS Entrypoint **solo cuando** `leyesabiertas_protocol` es igual a `http` y `leyesabiertas_host` está definido en las cabeceras de la llamada (Host Header).
* HTTPS Entrypoint: Traefik escucha en 0.0.0.0:443 **solo cuando** `leyesabiertas_protocol` es igual a `https`. Llamadas con la cabecera Host Header igual a `leyesabiertas_host` son dirigidas a los backends disponibles.

**HTTPS con certificado y llave**

Si `leyesabiertas_protocol` es igual a `https` y `enable_own_certificate` es igual a `true`, entonces será necesario proveer las rutas absolutas a los archivos del certificado y la llave con las variables `https_certificate_path` y `https_key_path` para poder pasar estos archivos a Traefik (ambos montados como volumenes docker).

**HTTPS con Let's Encrypt**

[Let's Encrypt](https://letsencrypt.org) esta integrado de manera nativa en Traefik y es extremadamente fácil de configurar. Si `leyesabiertas_protocol` es igual a `https` y `enable_lets_encrypt` es igual a `true`, entonces será necesario proveer una dirección de email con la variable `lets_encrypt_email`. Esto asumiendo que el nombre y resolución DNS ya fueron configurados previamente.

### MGOB
Al ser usado los backups automáticos de MongoDB son realizados con [MGOB](https://github.com/stefanprodan/mgob) que es otra excelente pieza de software implementada en Go. Por defecto está configurada para el backup de `leyesabiertas_database_name` a las 23:59 hs cada día, mantiene 10 backups localmente y timeout a 60 segundos. Este comportamiento es definido en el template `templates/leyesabiertas.yml.j2`.

La instalación por defecto incluye las siguientes rutas **solo disponibles** mediante localhost, puedes usar `ssh -L 8000:127.0.0.1:8000 user@server1` para acceder servicios de localhost en tu navegador, mas acerca de port forwarding con SSH [aquí](https://www.ssh.com/ssh/tunneling/example).

```
http://localhost:8000/status -> MGOB API /status
http://localhost:8000/version -> MGOB API /version
http://localhost:8000/storage -> MGOB API /storage # NOT TESTED
http://localhost:8000/metrics -> MGOB API /metrics to be used with Prometheus # NOT TESTED
http://localhost:8000/debug -> MGOB API /debug # NOT TESTED
http://localhost:8000/backup -> MGOB API /backup # NOT TESTED
```

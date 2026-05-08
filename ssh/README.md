## 🔑 SSH en NetBSD 10.1 con llave pública
Es importante ingresar al servidor remotamente de manera segura, por defecto **ssh** viene en la base del sistema, por tanto solamente debemos activar el servicio cuando se configura detalles en el proceso de la instalación, el servicio sshd ya funcionará con autenticación utilizando contraseña o password, es lo tradicional, pero en este tutorial utilizaremos un método más seguro, llaves.

### ✅ Ubicar el directorio donde están los archivos de configuración
Busquemos por ejemplo el archivo principal sshd_config

```bash
sudo find / -name sshd_config
```

Observamos que la ruta al directorio principal es */etc/ssh/sshd_config*, en la carpeta ssh no existe el directorio modular sshd_config.d (para evitar modificar el archivo principal), entonces debemos crear manualmente, en el siguiente paso.

### ✅ Crear el directorio modular sshd_config.d

```bash
sudo mkdir -p /etc/ssh/sshd_config.d
```

> [!IMPORTANT]
> Hay un detalle importante a considerar, en el archivo principal no existe una línea Include /etc/ssh/sshd_config.d, se tiene que escribir, pero eso implica editar, no deseamos hacer ello, por tanto NetBSD nos dá la opción de leer archivos para los servicios desde rc.conf, entonces para sshd le diremos que lea un archivo que es la copia del archivo principal, ese archivo lo llamaremos sshd_config.custom.


### ✅ Crear el archivo sshd_config.custom

```bash
$sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.custom
```

### ✅ Agregar el archivo en /etc/rc.conf
El archivo rc.conf contiene los servicios que se inician en tiempo de "boot time", aprovechamos una sentencia.

```text
sshd_flags="-f /ect/ssh/sshd_config.custom"
```

### ✅ Agregar la línea al archivo sshd_config.custom
Escribir al **inicio** del archivo.

```text
Include /etc/ssh/sshd_config.d/*.conf
```

Con la línea anterior le indicamos que lea archivos en la ruta indicada, todo archivo que termine en .conf, de preferencia poner números secuenciales, según la importancia, 10, 11, 11, se leerá primero 10, luego 11 y asi.

### ✅ Crear el archivo con el módulo de seguridad que se requiere

```bash
$sudo touch /etc/ssh/sshd_config.d/90-custom.conf
```

Ahora debemos escribir dentro del archivo anterior las sentencias para sshd.

```ssh
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no
AuthenticationMethods publickey
PermitRootLogin no
```

### ✅ Generar llaves pública y privada, copiar la llave pública al servidor
Depende desde qué terminal o host usted se conectará al servidor, en el caso que usted utilice un sistema operativo cliente GNU/Linux, usted puede generar la llave con el cliente ssh que la mayoría de distribuciones ya trae en la base, pero si usted usa una terminal windows la sugerencia es utilizar el software puttygen, en este tutorial indicamos los pasos para generar las llaves pública y privada, la llave .pub (publica) se debe subir al servidor, al directorio de un usuario que pertenece al sistema, veamos un "cómo".

#### Generar las llaves
El comando es ejecutado sin permiso de root, en la terminal del cliente se va a generar dos archivos en .ssh, en el directorio del usuario /home/tu_usuario, con el comando ls -a puedes observar los directorios ocultos y puedes mirar a .ssh

```bash
$ssh-keygen -t ed25519 -C "webserver-1"
```

> [!NOTE]
> -t es el tipo de algoritmo matemático y -C es el comentario, es necesario para identificar la llave de manera fácil.

Con le comando ls .ssh podemos observar dos archivos con nombres típicos: id_edxxxx y id_edxxxx.pub, éste último debemos subir al servidor.

#### Subir las llaves desde el cliente al servidor
Utilizaremos nuevamente al cliente ssh

```bash
ssh-copy-id ~/.ssh/id_edxxxx.pub usuario_server@ip-servidor
```

El caracter ~ (colita de puerquito) se obtiene con la combinación de teclas altgr+4, altgr esta a la dercha del teclado. preguntará la contraseña, por última vez, del usuario del servidor; si todo está bien copiará la llave en una línea en el archivo .ssh/authorized_keys e el servidor.

### Reiniciar el servicio sshd

```bash
$sudo service sshd restart
```

Ahora ya no ingreasarás por contraseña, será por lave, directo el ingreso al servidor.

### Ingresar al servidor

```bash
ssh usuario_servidor@ip-servidor
```

---
Gracias por llegar hasta aquí, lo escribí con mucho cariño, [@nevadoya](https://github.com/nevadoya)

## :hammer_and_wrench: Sudo en NetBSD 10.1
Los pasos necesarios para hacer funcionar sudo, es el comando que permite ejecutar comandos como root, en todo el proceso antes de habilitar sudo a los usuarios se debe usar a root (#) como usuario que realiza la configuración. Mencionamos que en la instalación del sistema operativo se ha creado un usuario el cual se agregó al grupo wheel. El grupo **wheel** es importante, los usuarios deben pertenecer a este grupo para utilizar sudo.

### ✅ Instalar sudo
Utlizaremos pkgin, el gestor de paquetes en NetBSD.

```bash
pkgin install sudo
```

### ✅ Ubicar el archivo principal sudoers y observar su configuración

```bash
find / -name sudoers
```

Observamos que la ruta donde está el archivo es /usr/pkg/etc/sudoers

### ✅ Observar si la línea %wheel está activa

```bash
grep "%wheel" /usr/pkg/etc/sudoers
```

Observamos que por defecto está comentada (#), entonces se necesita habilitar, pero nosotros seguiremos la configuración modular, no tocar los archivos principales. Utilizaremos el directorio **sudoers.d**, también aclaramos que si usamos sudo con el usuario que se ha creado en la instalación, el cual pertenece al grupo wheel, no puede usar sudo, el mensaje indica *is not in the sudoers file*.

### ✅ Crear archivos en sudoers.d

> [!CAUTION]
> Se recomienda usar **visudo**, porque es necesrio revisar la sintaxis.

```bash
visudo -f /usr/pkg/etc/sudoers.d/10-wheel-group
```

Es necesario -f y un nombre para el archivo, luego insertar las siguiente línea.

```text
# Grupo wheel
%wheel ALL=(ALL:ALL) ALL
```

Con ello le decimos que todo usuario que pertenece al grupo wheel puede usar sudo, la primera línea es un comentario, en archivos de configuración # generalmente es comentario.

### ✅ Verificar el funcionamiento de sudo
Hay un usuario estándar que se ha creado en la instalación, ese usuario ya pertenece al grupo wheel, ya puede usar sudo, por ejemplo puede intentar refrescar pkgin.

> [!TIP]
> $ sudo -v, para verificar si ya puede usar sudo, le preguntará su contraseña (usuario), si es correcto no muestra ningún mensaje.

> [!TIP]
> $ sudo whoami, ese comando pregunta contraseña y si la contraseña del usuario es correcta muestra un mensaje *root*.
Entonces con toda seguridad puede ejecutar algún comando que requiere privilegios de *root*.
> ¿quieres limpiar o reset sudo y que pida contraseña? Utiliza $sudo -k

```bash
$sudo pkgin update
```

Le preguntará su contraseña, si es la correcta podrá refrescar el repositorio de pkgin.

### ✅ Mensaje luego de una contraseña incorrecta
Podemos hacer divertido cuando la contraseña o password del usuario es incorrecto, creamos un archivo en el directorio modular sudoers.d que se llame 11-bad-password

```bash
visudo -f /usr/pkg/etc/sudoers.d/11-bad-password
```

Inserta esta líneas, si habilitas la primera los mensajes son aleatorios y en inglés, la segunda línea es un mensaje personalizado, cualquiera puede usar usted, he comentado la primera línea.

```text
#Defaults insults
Defaults badpass_message="Error, tu mensaje personalizado aquí. "
```
### ✅ Crear usuario y agregar al grupo wheel
Ahora que ya tenemos un usuario con permiso para usar sudo, entonces que cree un usuario nuevo y que añada al grupo wheel.

```bash
$sudo useradd -m -G wheel -s /bin/sh newuser
```

> [!NOTE]
> -m es necesario para crear el directorio en /home, -G agrega al grupo wheel de forma secundaria, no principal, -s es la shell que usará el usuario.
> para que funcione debes proporcionar una contraseña al usuario.

```bash
$sudo passwd newuser
```

No necesitas hacer más, ya existe una regla en 10-wheel-group y el usuario ya está habilitado para usar sudo.

### ✅ Crear usuario que no pueda usar sudo
Es más simple

```bash
$sudo useradd -m -s /bin/sh newuser2
$sudo passwd
```

Al intentar usar sudo le dirá que no pertenece al archivo sudoers.

> [!TIP]
> con el comando $id your_user, puedes mirar si pertenece al grupo wheel, después de crear el usuario debes cerrar sesión para ese usuario y volver a ingresar, $id your_user.

---

### 🏁 Conclusión
¡Felicidades! Has completado el tutorial. Si tienes dudas, puedes abrir un **Issue** en este repositorio.

---
Creado con ❤️ por [@nevadoya](https://github.com/nevadoya)

## 🔑 SSH en NetBSD 10.1 con llave pública
Es importante ingresar al servidor remotamente de manera segura, por defecto **ssh** viene en la base del sistema, por tanto solamente debemos activar el servicio cuando se configura detalles en el proceso de la instalación.

### ✅ Ubicar el directorio donde están los archivos de configuración
Busquemos por ejemplo el archivo principal sshd_config

```bash
sudo find / -name sshd_config
```

Observamos que la ruta al directorio principal es /etc/ssh/sshd_config, en la carpeta ssh no existe el directorio modular sshd_config.d, entonces debemos crear manualmente.







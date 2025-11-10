# Documentación: Despliegue y Configuración del Servidor FTP Seguro (vsftpd + FTPS)

## 1. Generación del certificado SSL autofirmado
Para habilitar FTPS (FTP seguro) se generó un certificado y clave privada:
```sql
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048
-keyout /etc/ssl/private/vsftpd.pem
-out /etc/ssl/private/vsftpd.pem
```
![Generación del certificado](../media/cano_creacion_certFTP.png)


## 2. Configuración del archivo `/etc/vsftpd.conf`

Se modificó el archivo principal de configuración para:

- Habilitar usuarios locales.
- Permitir operaciones de escritura.
- Desactivar usuarios anónimos.
- Activar SSL y forzar su uso en datos/autenticación.
- Especificar la ruta de los certificados generados.
- Desactivar protocolos SSL inseguros.

Fragmentos relevantes:
```sql
anonymous_enable=NO
local_enable=YES
write_enable=YES

rsa_cert_file=/etc/ssl/private/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd.pem
ssl_enable=YES
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_sslv2=NO
ssl_sslv3=NO
```
![Configuración SSL/TLS](../media/cano_confFTP2.png)
![Usuarios y permisos](../media/cano_confFTP1.png)


## 3. Reinicio y activación del servicio

Para que los cambios tomen efecto se reinició y activó el servicio al arranque del sistema:
```sql
sudo systemctl restart vsftpd
sudo systemctl enable vsftpd
sudo systemctl status vsftpd
```

![Reinicio y estado correcto del servicio](../media/cano_restart_statusFTP.png)

## 4. Prueba de conexión FTPS

Se utilizó el comando `lftp` para establecer una conexión segura FTPS al servidor y comprobar la operativa (listado de archivos, subida/descarga):
```sql
lftp ftps://usuario@IP_DEL_SERVIDOR
```

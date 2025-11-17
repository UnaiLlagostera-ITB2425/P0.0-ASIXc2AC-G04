# Tarea 5: Despliegue del servidor Web
---

## Información del hardwarenetwork:
  ethernets:
    enp1s0:
      dhcp4: true
    enp2s0:
      dhcp4: false
      addresses: [192.168.40.10/24]
    enp3s0:
      dhcp4: false
      addresses: [192.168.140.10/24]
  version: 2


El hardware utilizado fue preparado mediante isard con las siguientes características:

![Hardware](/media/hard_web.png)

![Hardware2](/media/hard_web2.png)

**Ip del Servidor Web (Apache + PHP):** 192.168.40.10/24

> Netplan:
```bash
network:
  ethernets:
    enp1s0:
      dhcp4: true
    enp2s0:
      dhcp4: false
      addresses: [192.168.40.10/24]
    enp3s0:
      dhcp4: false
      addresses: [192.168.140.10/24]
  version: 2

```

**Hostname del servidor web:** W-N04

>**Usuario:** bchecker
**Contraseña:** bchecker121

***

## Instalación de Apache y PHP en el servidor web

Ejecuta los siguientes comandos para instalar Apache2, PHP y los módulos necesarios para conectar con MySQL:

```bash
sudo apt update
sudo apt install apache2 php libapache2-mod-php php-mysql -y
```

Para verificar que PHP funciona correctamente, crea el archivo info.php y añadele algun texto formato html:

```bash
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
```

Luego abre en el navegador:

`http://192.168.40.10/info.php`

![Acceso web](/media/despl_web.png)

***

# Manual de Administración

**Versión:** 1.0  
**Fecha:** Noviembre 2025  
**Destinatarios:** Administradores del Sistema  
**Clasificación:** Interno - Confidencial

---

## Índice

1. [Introducción](#1-introducción)
2. [Arquitectura General del Sistema](#2-arquitectura-general-del-sistema)
3. [Topología y Diseño de Red](#3-topología-y-diseño-de-red)
4. [Infraestructura de Hardware](#4-infraestructura-de-hardware)
5. [Servidor Router (R-N04)](#5-servidor-router-r-n04)
6. [Servidor Web (W-N04)](#6-servidor-web-w-n04)
7. [Servidor Base de Datos (B-N04)](#7-servidor-base-de-datos-b-n04)
8. [Servidor FTP (F-N04)](#8-servidor-ftp-f-n04)
9. [Configuración de la Aplicación Web](#9-configuración-de-la-aplicación-web)
10. [Procedimientos Administrativos](#10-procedimientos-administrativos)
11. [Seguridad y Buenas Prácticas](#11-seguridad-y-buenas-prácticas)
12. [Monitorización y Mantenimiento](#12-monitorización-y-mantenimiento)
13. [Troubleshooting Avanzado](#13-troubleshooting-avanzado)
14. [Procedimientos de Recuperación](#14-procedimientos-de-recuperación)

---

## 1. Introducción

Este manual proporciona la documentación técnica completa para administradores y técnicos responsables de gestionar la Infraestructura Multicapa del Grupo 04. Incluye despliegue, configuración, mantenimiento, seguridad y procedimientos de recuperación.

**Audiencia**: Administradores de sistemas, técnicos de red, personal de operaciones.

---

## 2. Arquitectura General del Sistema

### 2.1 Descripción General

La infraestructura está compuesta por **4 servidores virtualizados** utilizando **Isard** como plataforma de virtualización, dividida en **2 subredes principales** para seguridad y gestión de tráfico.

**Subredes Principales**:
- **Nube de Servidores**: 192.168.40.0/24 (IPs fijas para infraestructura)
- **Nube de Clientes**: 192.168.140.0/24 (IPs dinámicas vía DHCP)

### 2.2 Componentes Principales

| Servidor | Hostname | IP Fija | Funciones | Sistema Operativo |
|----------|----------|---------|-----------|-------------------|
| Router/DHCP/DNS | R-N04 | 192.168.40.1 | Enrutamiento, NAT, DHCP, DNS | Ubuntu Server 22.04 |
| Servidor Web | W-N04 | 192.168.40.10 | Apache2, PHP, Aplicación Web | Ubuntu Server 22.04 |
| Base de Datos | B-N04 | 192.168.40.20 | MySQL Server, SSH | Ubuntu Server 22.04 |
| Servidor FTP | F-N04 | 192.168.40.30 | vsftpd, FTPS (FTP Seguro) | Ubuntu Server 22.04 |

### 2.3 Servicios de Red

- **Resolución de Nombres**: DNS (Bind9) en R-N04
- **Asignación de IPs**: DHCP (isc-dhcp-server) en R-N04
- **Enrutamiento**: IPTables/NAT en R-N04
- **Acceso Remoto Seguro**: SSH en B-N04
- **Transferencia Segura**: FTPS en F-N04
- **Aplicación Web**: PHP + Apache2 en W-N04

---

## 3. Topología y Diseño de Red

### 3.1 Segmentación de Red

**DMZ (Zona Desmilitarizada)**: 192.168.40.0/24
- Contiene servidores públicos (Web, FTP)
- Accesible desde redes externas para servicios específicos
- Aislada de la Intranet mediante firewall

**Intranet (Red Interna)**: 192.168.140.0/24
- Red privada para clientes y usuarios internos
- Acceso a Internet controlado mediante NAT
- Sin acceso directo a la DMZ por defecto

### 3.2 Flujo de Tráfico

1. **Clientes DHCP** en 192.168.140.0/24 reciben IP de R-N04
2. **Gateway por defecto**: 192.168.40.1 (R-N04)
3. **Servidor DNS**: 192.168.40.1 (primario) + 8.8.8.8 (secundario Google DNS)
4. **Servicios públicos**: Accesibles vía dominios (www.grup4.com, ftp.grup4.com)

### 3.3 Control de Acceso

```
Clientes (192.168.140.0/24) 
    ↓ (DHCP, DNS)
R-N04 (Router NAT)
    ↓ (Firewall rules)
    ├→ W-N04 (Puerto 80/443)
    ├→ F-N04 (Puerto 21/990)
    ├→ B-N04 (Puerto 3306 solo desde W-N04)
```

---

## 4. Infraestructura de Hardware

### 4.1 Especificaciones de Máquinas Virtuales (Isard)

Todas las máquinas han sido creadas mediante **Isard** con las siguientes características:

#### R-N04 (Router)
- **vCPU**: 2 cores
- **RAM**: 2 GB
- **Almacenamiento**: 20 GB
- **Interfaces de Red**: 3 (WAN + LAN + DMZ)
- **Sistema Operativo**: Ubuntu Server 22.04 LTS

#### W-N04 (Servidor Web)
- **vCPU**: 2 cores
- **RAM**: 2 GB
- **Almacenamiento**: 30 GB
- **Interfaces de Red**: 3 (WAN DHCP + Red Servidores + Red Clientes)
- **Sistema Operativo**: Ubuntu Server 22.04 LTS
- **Software**: Apache2, PHP, libapache2-mod-php, php-mysql

#### B-N04 (Base de Datos)
- **vCPU**: 2 cores
- **RAM**: 3 GB
- **Almacenamiento**: 40 GB (por base de datos)
- **Interfaces de Red**: 1 (Red Servidores)
- **Sistema Operativo**: Ubuntu Server 22.04 LTS
- **Software**: MySQL Server

#### F-N04 (Servidor FTP)
- **vCPU**: 2 cores
- **RAM**: 2 GB
- **Almacenamiento**: 50 GB
- **Interfaces de Red**: 1 (Red Servidores)
- **Sistema Operativo**: Ubuntu Server 22.04 LTS
- **Software**: vsftpd

### 4.2 Configuración de Interfaces de Red (Netplan)

#### Ejemplo W-N04:
```yaml
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

#### Ejemplo R-N04:
```yaml
network:
  ethernets:
    enp1s0:
      dhcp4: true  # WAN
    enp2s0:
      dhcp4: false
      addresses: [192.168.40.1/24]
    enp3s0:
      dhcp4: false
      addresses: [192.168.140.1/24]
  version: 2
```

---

## 5. Servidor Router (R-N04)

### 5.1 Funciones Principales

El router actúa como el núcleo de la infraestructura proporcionando:

- **Enrutamiento Inter-VLAN**: Comunica las subredes DMZ (40.0/24) e Intranet (140.0/24)
- **NAT (Network Address Translation)**: Permite a clientes internos acceder a Internet
- **DHCP**: Asigna IPs dinámicas en la red de clientes (192.168.140.0/24)
- **DNS**: Resuelve nombres de dominio internos mediante Bind9

### 5.2 Configuración de Servicios

#### NAT (IP Forwarding)

El router debe tener habilitado el reenvío de paquetes mediante `/etc/sysctl.conf`:

```
net.ipv4.ip_forward=1
```

**Reglas IPTables** para NAT:

```
- POSTROUTING: Enmascara IPs internas como IP del router (enp1s0)
- FORWARD: Permite tráfico entre interfaces
- Persistencia: iptables-persistent guarda las reglas en reinicio
```

#### DHCP (ISC-DHCP-Server)

**Configuración en `/etc/dhcp/dhcpd.conf`**:

```
Subred: 192.168.140.0/24
Rango: 192.168.140.10 - 192.168.140.50
Gateway: 192.168.140.1 (R-N04)
Servidores DNS: 192.168.40.1 (primario), 8.8.8.8 (secundario)
Dominio: grup4.com
Lease Time: 600 segundos (10 min default), 7200 max
```

**Interfaz configurada**: enp3s0

#### DNS (Bind9)

**Zonas configuradas**:

- **Directa**: grup4.com (resolve.hosts → IPs)
- **Inversa**: 40.168.192.in-addr.arpa (resolve IPs → hosts)

**Registros principales**:

```
router.grup4.com → 192.168.40.1
web.grup4.com → 192.168.40.10
bd.grup4.com → 192.168.40.20
ftp.grup4.com → 192.168.40.30
www.grup4.com → 192.168.40.10
```

**Forwarders externos**: 8.8.8.8, 8.8.4.4

---

## 6. Servidor Web (W-N04)

### 6.1 Componentes Instalados

- **Apache2**: Servidor web
- **PHP**: Lenguaje de programación del backend
- **libapache2-mod-php**: Módulo PHP para Apache
- **php-mysql**: Conector PHP a MySQL

### 6.2 Estructura de Archivos

```
/var/www/html/
├── index.php          # Página principal con tabla de datos
├── db.php             # Configuración de conexión a MySQL
├── owl.carousel.min.css
├── owl.theme.default.min.css
└── themify-icons.css
```

### 6.3 Archivo db.php (Configuración de BD)

Credenciales y conexión a MySQL:

```
Host: 192.168.40.20 (B-N04)
Usuario: bchecker
Contraseña: bchecker121
Base de Datos: crud_db
Tabla Principal: equipaments_educacio
```

**Configuración MySQL remota** en B-N04:

- Usuario `bchecker` autorizado desde 192.168.40.10
- Permisos: SELECT, INSERT, UPDATE, DELETE en crud_db
- Bind-address: 0.0.0.0 (escucha todas las interfaces)

### 6.4 Aplicación Web (index.php)

**Funcionalidad**:
- Conexión a base de datos MySQL
- Consulta tabla `equipaments_educacio`
- Renderización HTML de datos
- Protección contra XSS (htmlspecialchars)

**Datos mostrados**:
- ID de Registro, Nombre, Institución
- Dirección, Barrio, Distrito
- Código Postal, Ciudad, Fecha de Creación

**Limit**: 50 registros por página

---

## 7. Servidor Base de Datos (B-N04)

### 7.1 Base de Datos Principal

**Nombre**: crud_db

**Tabla Principal**: equipaments_educacio

**Campos**:
- register_id, name, institution_id, institution_name
- created, modified
- addresses_* (roadtype, road, neighborhood, district, zip, town, main_address, type)
- values_* (id, attribute_id, category)

### 7.2 Usuarios MySQL

| Usuario | Origen | Contraseña | Permisos | Uso |
|---------|--------|-----------|----------|-----|
| bchecker | 192.168.40.10 | bchecker121 | SELECT en crud_db | Aplicación Web |
| root | localhost | * | Todos | Administración |

### 7.3 Backup y Restore

**Ubicación recomendada**: `/backup/`

```bash
# Backup completo
mysqldump -u root -p crud_db > /backup/crud_db_backup.sql

# Restore
mysql -u root -p crud_db < /backup/crud_db_backup.sql
```

**Frecuencia recomendada**: Diaria a las 02:00 AM

### 7.4 Monitorización

Verificar:
- Espacio en disco: `df -h`
- Procesos MySQL: `systemctl status mysql`
- Conexiones activas: `SHOW PROCESSLIST;`
- Tamaño de tablas: `SHOW TABLE STATUS;`

---

## 8. Servidor FTP (F-N04)

### 8.1 Servicio vsftpd + FTPS

**Protocolo**: FTPS (FTP Seguro) - cifrado SSL/TLS

**Características**:
- Usuarios locales autenticados
- SSL/TLS obligatorio para control y datos
- Certificado autofirmado (365 días)
- Deshabilita protocolos inseguros (SSLv2, SSLv3)
- Sin acceso anónimo

### 8.2 Configuración (`/etc/vsftpd.conf`)

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| anonymous_enable | NO | Deshabilita acceso anónimo |
| local_enable | YES | Habilita usuarios locales |
| write_enable | YES | Permite uploads/writes |
| ssl_enable | YES | Activa SSL/TLS |
| force_local_logins_ssl | YES | SSL obligatorio para login |
| force_local_data_ssl | YES | SSL obligatorio para datos |
| rsa_cert_file | /etc/ssl/private/vsftpd.pem | Ruta certificado |
| rsa_private_key_file | /etc/ssl/private/vsftpd.pem | Ruta clave privada |

### 8.3 Certificado SSL

**Generado con**:
```
RSA 2048-bit, autofirmado
Validez: 365 días
Ubicación: /etc/ssl/private/vsftpd.pem
```

### 8.4 Gestión de Usuarios FTP

Los usuarios FTP son **usuarios del sistema Linux**. Crear nuevo usuario:

```bash
sudo useradd -m -s /bin/false usuario_ftp
sudo passwd usuario_ftp
```

El directorio home del usuario será su raíz FTP.

**Recomendación**: Crear usuarios específicos con shell restringido (`/bin/false`).

---

## 9. Configuración de la Aplicación Web

### 9.1 Flujo de Conexión

```
Usuario (navegador) 
  ↓ HTTP/HTTPS
W-N04 (Apache2 + PHP)
  ↓ MySQLi
B-N04 (MySQL Server)
  ↓ Datos
equipaments_educacio
```

### 9.2 Resolución de Nombres

**Para usuarios finales**:

Acceso vía dominio: `www.grup4.com` o `http://192.168.40.10`

**DNS Resolution**:
```
Cliente solicita: www.grup4.com
Router DNS (192.168.40.1) resuelve → 192.168.40.10
Conexión a Apache2 en W-N04
```

### 9.3 Control de Errores en PHP

**Configuración en index.php**:

```php
ini_set('display_errors', 1);
error_reporting(E_ALL);
```

Muestra errores en desarrollo. En producción, redirigir a archivo de log.

### 9.4 Seguridad de la Aplicación

- **Escaping de HTML**: htmlspecialchars() previene XSS
- **Manejo de NULL**: Operador `??` evita notices
- **Preparación de sentencias**: MySQLi (no preparadas actualmente, revisar)
- **Charset UTF-8**: Soporte a caracteres especiales

---

## 10. Procedimientos Administrativos

### 10.1 Gestión de Servicios

**Iniciar/Detener servicios**:

```bash
# Router (R-N04)
systemctl {start|stop|restart|status} bind9
systemctl {start|stop|restart|status} isc-dhcp-server
systemctl {start|stop|restart|status} networking

# Web (W-N04)
systemctl {start|stop|restart|status} apache2
systemctl {start|stop|restart|status} php-fpm

# Base de Datos (B-N04)
systemctl {start|stop|restart|status} mysql

# FTP (F-N04)
systemctl {start|stop|restart|status} vsftpd
```

### 10.2 Verificación de Conectividad

```bash
# Ping entre servidores
ping 192.168.40.1    # Router
ping 192.168.40.10   # Web
ping 192.168.40.20   # BBDD
ping 192.168.40.30   # FTP

# Resolución DNS
nslookup web.grup4.com 192.168.40.1
dig @192.168.40.1 www.grup4.com

# Conexión a bases de datos
mysql -h 192.168.40.20 -u bchecker -p -e "SELECT 1"

# Puertos abiertos
netstat -tlnp | grep LISTEN
```

### 10.3 Administración de Usuarios FTP

**Crear usuario**:
```bash
sudo useradd -m -s /bin/false usuario
sudo passwd usuario
```

**Eliminar usuario**:
```bash
sudo userdel -r usuario
```

**Cambiar contraseña**:
```bash
sudo passwd usuario
```

**Listar usuarios SFTP/FTP**:
```bash
cat /etc/passwd | grep -E '(usuarios_ftp|bin/false)'
```

---

## 11. Seguridad y Buenas Prácticas

### 11.1 Firewall y ACLs

**Recomendaciones**:

1. **DMZ (192.168.40.0/24)**:
   - Puerto 80 (HTTP): Abierto a público
   - Puerto 443 (HTTPS): Abierto a público
   - Puerto 21/990 (FTP/FTPS): Restringido a IPs autorizadas
   - Puerto 3306 (MySQL): SOLO desde 192.168.40.10

2. **Intranet (192.168.140.0/24)**:
   - Salida a Internet: Permitida (NAT)
   - Acceso a DMZ: Controlado
   - Acceso a BBDD: DENEGADO
   - SSH: Solo desde interfaces administrativas

### 11.2 Configuración de Contraseñas

**Contraseña común del sistema**:
- **Usuario**: bchecker
- **Contraseña**: bchecker121

> ⚠️ **CAMBIAR EN PRODUCCIÓN** - Usar contraseñas fuertes y únicas por servidor

### 11.3 Certificados SSL

**FTP**:
- Autofirmado para ambiente de prueba
- En producción, usar certificado válido de CA

**Web**:
- Considerar HTTPS con Let's Encrypt (gratuito)

### 11.4 Logs del Sistema

**Ubicaciones importantes**:

```
Apache: /var/log/apache2/
MySQL: /var/log/mysql/
DNS: /var/log/syslog (Bind9)
FTP: /var/log/vsftpd.log
Syslog: /var/log/syslog
```

**Rotación**: Configurar logrotate para evitar llenar disco

---

## 12. Monitorización y Mantenimiento

### 12.1 Chequeos Diarios

- ✓ Espacio en disco en todos los servidores
- ✓ Servicios activos y funcionando
- ✓ Conectividad de red
- ✓ Errores en logs del sistema

### 12.2 Chequeos Semanales

- ✓ Realizar backups de BBDD
- ✓ Revisar permisos de usuarios FTP
- ✓ Validación de certificados SSL (días restantes)
- ✓ Actualización de sistema (apt update)

### 12.3 Métricas Clave

**Para monitorizar**:

- CPU: Debe estar < 80%
- Memoria: Debe estar < 85%
- Disco: Debe estar < 90%
- Conexiones MySQL: < 100 simultáneas
- Ancho de banda: Según SLA

### 12.4 Herramientas Recomendadas

- **Monitorización**: Prometheus + Grafana
- **Alertas**: AlertManager
- **Logs centralizados**: ELK Stack
- **Gestión**: Ansible para automatización

---

## 13. Troubleshooting Avanzado

### 13.1 Router no enruta tráfico

**Síntomas**: Clientes no pueden acceder a servidores

**Diagnóstico**:
```bash
# Verificar interfaces activas
ip addr show

# Verificar rutas
ip route show

# Verificar iptables
iptables -t nat -L -v

# Verificar ip_forward
cat /proc/sys/net/ipv4/ip_forward
```

**Solución**:
- Habilitar ip_forward: `echo 1 > /proc/sys/net/ipv4/ip_forward`
- Verificar sintaxis de iptables
- Reiniciar networking: `systemctl restart networking`

### 13.2 DNS no resuelve

**Síntomas**: nslookup falla, ping a hosts por nombre no funciona

**Diagnóstico**:
```bash
# Comprobar si Bind9 está activo
systemctl status bind9

# Validar configuración
named-checkconf
named-checkzone grup4.com /etc/bind/db.grup4.com

# Hacer queries
dig @192.168.40.1 web.grup4.com
nslookup www.grup4.com 192.168.40.1
```

**Solución**:
- Comprobar archivos de zona (serial, sintaxis)
- Reiniciar Bind9
- Verificar puertos abiertos (53)

### 13.3 DHCP no asigna IPs

**Síntomas**: Clientes no reciben IP automática

**Diagnóstico**:
```bash
# Estado del servicio
systemctl status isc-dhcp-server

# Revisar logs
journalctl -u isc-dhcp-server -n 50

# Validar configuración
dhcpd -t -cf /etc/dhcp/dhcpd.conf
```

**Solución**:
- Verificar que interfaz en `/etc/default/isc-dhcp-server` es correcta
- Comprobar que rango está en la subred correcta
- Revisar archivos lease: `/var/lib/dhcp/`

### 13.4 Conexión Web a BBDD falla

**Síntomas**: Error "Conexión fallida" en index.php

**Diagnóstico**:
```bash
# Desde W-N04, probar conexión:
mysql -h 192.168.40.20 -u bchecker -p

# Ver procesos MySQL activos
mysql -h 192.168.40.20 -u root -p -e "SHOW PROCESSLIST;"

# Revisar logs MySQL
tail -f /var/log/mysql/error.log
```

**Solución**:
- Verificar que bchecker@192.168.40.10 está creado
- Comprobar bind-address = 0.0.0.0 en MySQL
- Reiniciar MySQL: `systemctl restart mysql`
- Verificar firewall entre servidores

### 13.5 FTP usuarios no pueden conectar

**Síntomas**: Conexión denegada, timeout

**Diagnóstico**:
```bash
# Estado vsftpd
systemctl status vsftpd

# Usuario existe
id usuario_ftp

# Revisiones en directorio home
ls -la /home/usuario_ftp

# Probar conexión con lftp
lftp -u usuario ftps://192.168.40.30
```

**Solución**:
- Usuario debe existir en el sistema
- Shell debe ser /bin/false (sin login)
- Permisos del home: usuario debe ser propietario
- Puerto 21 debe estar abierto

---

## 14. Procedimientos de Recuperación

### 14.1 Backup de Configuraciones

**Crear snapshots** de máquinas virtuales en Isard regularmente.

**Archivos críticos a backupear**:

```
R-N04:
  /etc/bind/ (DNS zones)
  /etc/dhcp/ (DHCP config)
  /etc/sysctl.conf (IP forward)
  /etc/iptables/ (NAT rules)

W-N04:
  /var/www/html/ (Aplicación)
  /etc/apache2/ (Configuración Apache)

B-N04:
  /var/lib/mysql/ (Datafiles MySQL)
  Dumps SQL: mysqldump

F-N04:
  /etc/vsftpd.conf
  /etc/ssl/private/vsftpd.pem
  /home/* (Datos de usuarios)
```

### 14.2 Restauración Rápida

**Si un servidor se cae**:

1. Recuperar VM desde snapshot en Isard
2. Iniciar máquina
3. Verificar servicios: `systemctl status [servicio]`
4. Comprobar conectividad
5. Validar datos/configuración

**Si se pierde BBDD**:

1. Detener Apache: `systemctl stop apache2`
2. Restaurar desde último backup: `mysql crud_db < backup.sql`
3. Verificar usuarios MySQL
4. Reiniciar Apache

### 14.3 Cambios de Configuración

Antes de cambios:

1. Crear snapshot de VM en Isard
2. Documentar cambio a realizar
3. Tener comando para deshacer
4. Probar en ambiente de test primero
5. Ejecutar en producción
6. Validar funcionamiento

---

## Apéndice: Comandos de Diagnóstico Útiles

```bash
# Red
nmtui                    # Interfaz gráfica de red
ip addr show
ip route show
netstat -tlnp
ss -tulpn

# DNS
dig web.grup4.com @192.168.40.1
nslookup www.grup4.com
named-checkconf -z

# DHCP
dhclient -v eth0         # Solicitar DHCP
lease details

# MySQL
mysql -u root -p
SHOW SLAVE STATUS;
SHOW PROCESSLIST;

# Apache
apache2ctl -S            # Virtual hosts
apache2ctl -M            # Módulos cargados
systemctl reload apache2 # Sin desconectar

# Sistema
df -h                    # Espacio disco
top                      # Procesos
journalctl -f            # Logs en tiempo real
sysctl -a | grep         # Parámetros kernel
```

---

## Información de Contacto Técnico

**Equipo de Infraestructura - Grupo 04**

- **Soporte Técnico**: soporte@grup4.com
- **Emergencias**: número-de-empresa
- **Horario**: Lunes a Viernes, 8:00 - 20:00 h

---

**Manual de Administración - Infraestructura Multicapa Grupo 04**  
*Documento controlado - Versión 1.0*  
*© 2025 Grupo 04. Todos los derechos reservados.*  
*Acceso restringido a personal administrativo autorizado.*

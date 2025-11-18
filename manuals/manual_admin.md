# Manual de Administración

## Infraestructura Multicapa - Grupo 04

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
8. [Servidor SFTP (F-N04)](#8-servidor-sftp-f-n04)
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
- **Nube de Servidores (DMZ)**: 192.168.40.0/24 (IPs fijas para servicios públicos)
- **Nube Intranet**: 192.168.140.0/24 (IPs dinámicas vía DHCP + Servidor BBDD)

### 2.2 Componentes Principales

| Servidor | Hostname | IP Fija | Subred | Funciones | Sistema Operativo |
|----------|----------|---------|--------|-----------|-------------------|
| Router/DHCP/DNS | R-N04 | 192.168.40.1 | DMZ | Enrutamiento, NAT, DHCP, DNS | Ubuntu Server 22.04 |
| Servidor Web | W-N04 | 192.168.40.10 | DMZ | Apache2, PHP, Aplicación Web | Ubuntu Server 22.04 |
| Base de Datos | B-N04 | 192.168.140.20 | Intranet | MySQL Server, SSH | Ubuntu Server 22.04 |
| Servidor SFTP | F-N04 | 192.168.40.30 | DMZ | OpenSSH, SFTP (SSH File Transfer) | Ubuntu Server 22.04 |

### 2.3 Servicios de Red

- **Resolución de Nombres**: DNS (Bind9) en R-N04
- **Asignación de IPs**: DHCP (isc-dhcp-server) en R-N04
- **Enrutamiento**: IPTables/NAT en R-N04
- **Base de Datos**: MySQL Server en B-N04 (accesible desde Intranet)
- **Acceso Remoto Seguro**: SSH en B-N04 y F-N04
- **Transferencia Segura**: SFTP (SSH) en F-N04
- **Aplicación Web**: PHP + Apache2 en W-N04

---

## 3. Topología y Diseño de Red

### 3.1 Segmentación de Red

**DMZ (Zona Desmilitarizada)**: 192.168.40.0/24
- Contiene servidores públicos (Web, SFTP, Router)
- Accesible desde redes externas para servicios específicos
- Aislada de la Intranet mediante firewall controlado

**Intranet (Red Privada)**: 192.168.140.0/24
- Red privada para clientes internos y servidor BBDD
- Acceso a Internet controlado mediante NAT a través de R-N04
- Base de datos en esta subred para mayor seguridad

### 3.2 Flujo de Tráfico

1. **Clientes DHCP** en 192.168.140.0/24 reciben IP de R-N04
2. **Gateway por defecto**: 192.168.40.1 (R-N04) con rutas configuradas
3. **Servidor DNS**: 192.168.40.1 (primario) + 8.8.8.8 (secundario Google DNS)
4. **Servicios públicos**: Accesibles vía dominios (www.grup4.com, ftp.grup4.com)
5. **Servidor BBDD**: Accesible desde W-N04 (192.168.40.10) hacia 192.168.140.20

### 3.3 Control de Acceso

```
Clientes Intranet (192.168.140.0/24)
    ↓ (DHCP, DNS, HTTP, SFTP)
R-N04 (Router NAT)
    ↓ (Firewall rules)
    ├→ W-N04 (Puerto 80/443)
    ├→ F-N04 (Puerto 2222 SFTP)
    └→ B-N04 (Puerto 3306 desde W-N04, restringido por firewall)
    
W-N04 (Servidor Web en DMZ)
    ↓ (Conexión remota MySQL)
B-N04 (Servidor BBDD en Intranet)
```

---

## 4. Infraestructura de Hardware

### 4.1 Especificaciones de Máquinas Virtuales (Isard)

Todas las máquinas han sido creadas mediante **Isard** con las siguientes características:

#### R-N04 (Router)
- **vCPU**: 2 cores
- **RAM**: 2 GB
- **Almacenamiento**: 20 GB
- **Interfaces de Red**: 3 (WAN + DMZ + Intranet)
- **Sistema Operativo**: Ubuntu Server 22.04 LTS

#### W-N04 (Servidor Web)
- **vCPU**: 2 cores
- **RAM**: 2 GB
- **Almacenamiento**: 30 GB
- **Interfaces de Red**: 2 (WAN DHCP + DMZ fija)
- **Sistema Operativo**: Ubuntu Server 22.04 LTS
- **Software**: Apache2, PHP, libapache2-mod-php, php-mysql

#### B-N04 (Base de Datos)
- **vCPU**: 2 cores
- **RAM**: 3 GB
- **Almacenamiento**: 40 GB (por base de datos)
- **Interfaces de Red**: 1 (Intranet)
- **Sistema Operativo**: Ubuntu Server 22.04 LTS
- **Software**: MySQL Server

#### F-N04 (Servidor SFTP)
- **vCPU**: 2 cores
- **RAM**: 2 GB
- **Almacenamiento**: 50 GB
- **Interfaces de Red**: 1 (DMZ)
- **Sistema Operativo**: Ubuntu Server 22.04 LTS
- **Software**: OpenSSH Server

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

#### Ejemplo B-N04 (Base de Datos en Intranet):
```yaml
network:
  ethernets:
    enp1s0:
      dhcp4: false
      addresses: [192.168.140.20/24]
      gateway4: 192.168.140.1
      nameservers:
        addresses: [192.168.40.1, 8.8.8.8]
  version: 2
```

#### Ejemplo F-N04 (SFTP en DMZ):
```yaml
network:
  ethernets:
    enp1s0:
      dhcp4: false
      addresses: [192.168.40.30/24]
      gateway4: 192.168.40.1
      nameservers:
        addresses: [192.168.40.1, 8.8.8.8]
  version: 2
```

---

## 5. Servidor Router (R-N04)

### 5.1 Funciones Principales

El router actúa como el núcleo de la infraestructura proporcionando:

- **Enrutamiento Inter-VLAN**: Comunica las subredes DMZ (40.0/24) e Intranet (140.0/24)
- **NAT (Network Address Translation)**: Permite a clientes y servidores internos acceder a Internet
- **DHCP**: Asigna IPs dinámicas en la red interna (192.168.140.0/24)
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
- FORWARD: Permite tráfico entre interfaces (DMZ ↔ Intranet controlado)
- INPUT: Acepta tráfico para servicios locales
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
- **Inversa**: 40.168.192.in-addr.arpa y 140.168.192.in-addr.arpa

**Registros principales**:

```
router.grup4.com → 192.168.40.1
web.grup4.com → 192.168.40.10
bd.grup4.com → 192.168.140.20
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

Credenciales y conexión a MySQL (remota en Intranet):

```
Host: 192.168.140.20 (B-N04)
Puerto: 3306
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
- Conexión remota a base de datos MySQL en Intranet
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

### 7.1 Ubicación y Red

- **IP**: 192.168.140.20 (Intranet)
- **Gateway**: 192.168.140.1 (R-N04)
- **Ubicación física**: Red privada, no en DMZ
- **Acceso**: Solo desde W-N04 (192.168.40.10) a través de rutas configuradas

### 7.2 Base de Datos Principal

**Nombre**: crud_db

**Tabla Principal**: equipaments_educacio

**Campos**:
- register_id, name, institution_id, institution_name
- created, modified
- addresses_* (roadtype, road, neighborhood, district, zip, town, main_address, type)
- values_* (id, attribute_id, category)

### 7.3 Usuarios MySQL

| Usuario | Origen | Contraseña | Permisos | Uso |
|---------|--------|-----------|----------|-----|
| bchecker | 192.168.40.10 | bchecker121 | SELECT en crud_db | Aplicación Web |
| root | localhost | * | Todos | Administración |

### 7.4 Backup y Restore

**Ubicación recomendada**: `/backup/`

```bash
# Backup completo
mysqldump -u root -p crud_db > /backup/crud_db_backup.sql

# Restore
mysql -u root -p crud_db < /backup/crud_db_backup.sql
```

**Frecuencia recomendada**: Diaria a las 02:00 AM

### 7.5 Monitorización

Verificar:
- Espacio en disco: `df -h`
- Procesos MySQL: `systemctl status mysql`
- Conexiones activas: `SHOW PROCESSLIST;`
- Tamaño de tablas: `SHOW TABLE STATUS;`

---

## 8. Servidor SFTP (F-N04)

### 8.1 Servicio OpenSSH + SFTP

**Protocolo**: SFTP (SSH File Transfer Protocol)

**Características**:
- Basado en OpenSSH Server
- Todo el tráfico cifrado mediante SSH
- Autenticación por contraseña y/o clave pública
- Puerto personalizado: **2222** (en lugar del 22 estándar)
- Modo pasivo configurado: puertos 40000-50000
- Jaula chroot para usuarios SFTP

### 8.2 Configuración (`/etc/ssh/sshd_config`)

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| Port | 2222 | Puerto personalizado para SSH/SFTP |
| PermitRootLogin | no | Deshabilita login directo como root |
| PasswordAuthentication | yes | Permite autenticación por contraseña |
| PubkeyAuthentication | yes | Permite autenticación por clave pública |
| Subsystem sftp | internal-sftp | Habilita subsistema SFTP interno |
| Match Group sftpusers | - | Configuración específica para grupo |
| ChrootDirectory | /home/%u | Jaula chroot por usuario |
| ForceCommand | internal-sftp | Fuerza uso de SFTP (no shell) |
| PermitTunnel | no | Deshabilita túneles |
| AllowAgentForwarding | no | Deshabilita forwarding de agente |
| AllowTcpForwarding | no | Deshabilita forwarding TCP |
| X11Forwarding | no | Deshabilita X11 forwarding |

### 8.3 Configuración de Modo Pasivo

**Rango de puertos pasivos**: 40000-50000

Configuración en `/etc/ssh/sshd_config` o archivo adicional:
```
# Configuración adicional para SFTP
PassivePorts 40000 50000
```

**Firewall debe permitir**:
- Puerto 2222 (conexión de control SSH/SFTP)
- Puertos 40000-50000 (transferencias de datos en modo pasivo)

### 8.4 Gestión de Usuarios SFTP

Los usuarios SFTP son **usuarios del sistema Linux** con restricciones especiales.

**Crear nuevo usuario SFTP**:

```bash
# Crear grupo sftpusers (si no existe)
sudo groupadd sftpusers

# Crear usuario
sudo useradd -m -g sftpusers -s /bin/false usuario_sftp

# Establecer contraseña
sudo passwd usuario_sftp

# Ajustar permisos del home (requerido para chroot)
sudo chown root:root /home/usuario_sftp
sudo chmod 755 /home/usuario_sftp

# Crear directorio de uploads dentro del home
sudo mkdir /home/usuario_sftp/uploads
sudo chown usuario_sftp:sftpusers /home/usuario_sftp/uploads
sudo chmod 755 /home/usuario_sftp/uploads
```

**Eliminar usuario**:
```bash
sudo userdel -r usuario_sftp
```

**Cambiar contraseña**:
```bash
sudo passwd usuario_sftp
```

**Listar usuarios SFTP**:
```bash
getent group sftpusers
```

### 8.5 Autenticación por Clave Pública (Opcional)

Para mayor seguridad, se puede configurar autenticación por clave SSH:

```bash
# En el cliente, generar par de claves
ssh-keygen -t rsa -b 4096 -C "usuario@dominio.com"

# Copiar clave pública al servidor
ssh-copy-id -p 2222 usuario_sftp@192.168.40.30

# O manualmente, agregar clave a:
# /home/usuario_sftp/.ssh/authorized_keys
```

### 8.6 Reiniciar Servicio

Después de cambios en configuración:

```bash
sudo systemctl restart ssh
sudo systemctl status ssh
```

---

## 9. Configuración de la Aplicación Web

### 9.1 Flujo de Conexión

```
Usuario (navegador en Intranet)
  ↓ HTTP/HTTPS
W-N04 (Apache2 + PHP en 192.168.40.10)
  ↓ MySQLi (conexión remota a Intranet)
R-N04 (Router - NAT entre subredes)
  ↓ 
B-N04 (MySQL Server en 192.168.140.20)
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
PHP solicita bd.grup4.com → 192.168.140.20
```

### 9.3 Comunicación DMZ ↔ Intranet

La comunicación entre W-N04 (DMZ) y B-N04 (Intranet) es controlada:
- Requiere rutas específicas en R-N04
- Puerto 3306 permitido solo desde 192.168.40.10
- Firewall debe estar configurado para permitir este tráfico

### 9.4 Control de Errores en PHP

**Configuración en index.php**:

```php
ini_set('display_errors', 1);
error_reporting(E_ALL);
```

Muestra errores en desarrollo. En producción, redirigir a archivo de log.

### 9.5 Seguridad de la Aplicación

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

# SFTP (F-N04)
systemctl {start|stop|restart|status} ssh
```

### 10.2 Verificación de Conectividad

```bash
# Ping entre servidores
ping 192.168.40.1    # Router (DMZ)
ping 192.168.40.10   # Web (DMZ)
ping 192.168.140.20  # BBDD (Intranet)
ping 192.168.40.30   # SFTP (DMZ)

# Resolución DNS
nslookup bd.grup4.com 192.168.40.1
dig @192.168.40.1 www.grup4.com
dig @192.168.40.1 ftp.grup4.com

# Conexión a bases de datos desde W-N04
mysql -h 192.168.140.20 -u bchecker -p -e "SELECT 1"

# Prueba de SFTP
sftp -P 2222 usuario@192.168.40.30

# Puertos abiertos
netstat -tlnp | grep LISTEN
ss -tlnp | grep 2222
```

### 10.3 Administración de Usuarios SFTP

**Crear usuario completo**:
```bash
sudo groupadd sftpusers  # Si no existe
sudo useradd -m -g sftpusers -s /bin/false usuario
sudo passwd usuario
sudo chown root:root /home/usuario
sudo chmod 755 /home/usuario
sudo mkdir /home/usuario/uploads
sudo chown usuario:sftpusers /home/usuario/uploads
sudo systemctl restart ssh
```

**Eliminar usuario**:
```bash
sudo userdel -r usuario
sudo systemctl restart ssh
```

**Cambiar contraseña**:
```bash
sudo passwd usuario
```

**Listar usuarios SFTP**:
```bash
getent group sftpusers
cat /etc/passwd | grep sftpusers
```

**Ver sesiones activas**:
```bash
who
w
last -a | grep sftp
```

---

## 11. Seguridad y Buenas Prácticas

### 11.1 Firewall y ACLs

**Recomendaciones**:

1. **DMZ (192.168.40.0/24)**:
   - Puerto 80 (HTTP): Abierto a público
   - Puerto 443 (HTTPS): Abierto a público
   - **Puerto 2222 (SFTP)**: Restringido a IPs autorizadas o abierto con autenticación fuerte
   - **Puertos 40000-50000 (SFTP pasivo)**: Permitir para transferencias de datos
   - Puerto 3306 (MySQL): NUNCA directamente desde DMZ

2. **Intranet (192.168.140.0/24)**:
   - Salida a Internet: Permitida (NAT)
   - Acceso desde DMZ: Controlado (solo W-N04 → BBDD en puerto 3306)
   - Acceso a DMZ: Controlado
   - SSH: Solo desde interfaces administrativas
   - MySQL: Solo escucha en 0.0.0.0 pero firewall restringe

### 11.2 Configuración de Contraseñas

**Contraseña común del sistema**:
- **Usuario**: bchecker
- **Contraseña**: bchecker121

> ⚠️ **CAMBIAR EN PRODUCCIÓN** - Usar contraseñas fuertes y únicas por servidor

### 11.3 Seguridad SSH/SFTP

**Buenas prácticas implementadas**:
- Puerto no estándar (2222)
- Root login deshabilitado
- Jaula chroot para usuarios SFTP
- Sin shell para usuarios SFTP (/bin/false)
- Forwarding deshabilitado

**Recomendaciones adicionales**:
- Considerar autenticación por clave pública
- Implementar fail2ban para protección contra fuerza bruta
- Limitar intentos de login
- Monitorizar logs de autenticación

### 11.4 Logs del Sistema

**Ubicaciones importantes**:

```
Apache: /var/log/apache2/
MySQL: /var/log/mysql/
DNS: /var/log/syslog (Bind9)
SSH/SFTP: /var/log/auth.log
Syslog: /var/log/syslog
```

**Monitorizar accesos SFTP**:
```bash
tail -f /var/log/auth.log | grep sftp
```

**Rotación**: Configurar logrotate para evitar llenar disco

---

## 12. Monitorización y Mantenimiento

### 12.1 Chequeos Diarios

- ✓ Espacio en disco en todos los servidores
- ✓ Servicios activos y funcionando
- ✓ Conectividad de red (DMZ e Intranet)
- ✓ Errores en logs del sistema
- ✓ Conectividad W-N04 → B-N04
- ✓ Logs de autenticación SFTP

### 12.2 Chequeos Semanales

- ✓ Realizar backups de BBDD
- ✓ Revisar usuarios y permisos SFTP
- ✓ Validar configuración SSH
- ✓ Actualización de sistema (apt update)
- ✓ Revisar logs de conexiones MySQL
- ✓ Verificar espacio en directorios SFTP

### 12.3 Métricas Clave

**Para monitorizar**:

- CPU: Debe estar < 80%
- Memoria: Debe estar < 85%
- Disco: Debe estar < 90%
- Conexiones MySQL: < 100 simultáneas
- Sesiones SFTP simultáneas: < 50
- Ancho de banda DMZ-Intranet: Según SLA

### 12.4 Herramientas Recomendadas

- **Monitorización**: Prometheus + Grafana
- **Alertas**: AlertManager
- **Logs centralizados**: ELK Stack
- **Gestión**: Ansible para automatización
- **Seguridad SSH**: fail2ban

---

## 13. Troubleshooting Avanzado

### 13.1 Router no enruta tráfico DMZ-Intranet

**Síntomas**: W-N04 no puede conectar con B-N04

**Diagnóstico**:
```bash
# Verificar interfaces activas
ip addr show

# Verificar rutas
ip route show

# Verificar iptables (DMZ ↔ Intranet)
iptables -t nat -L -v
iptables -t filter -L -v

# Verificar ip_forward
cat /proc/sys/net/ipv4/ip_forward
```

**Solución**:
- Habilitar ip_forward: `echo 1 > /proc/sys/net/ipv4/ip_forward`
- Verificar sintaxis de iptables para inter-VLAN
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
nslookup bd.grup4.com 192.168.40.1
nslookup ftp.grup4.com 192.168.40.1
```

**Solución**:
- Comprobar archivos de zona (serial, sintaxis)
- Reiniciar Bind9
- Verificar puertos abiertos (53)

### 13.3 DHCP no asigna IPs

**Síntomas**: Clientes no reciben IP automática en Intranet

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
- Verificar que interfaz en `/etc/default/isc-dhcp-server` es enp3s0
- Comprobar que rango está en 192.168.140.0/24
- Revisar archivos lease: `/var/lib/dhcp/`

### 13.4 Conexión Web a BBDD falla

**Síntomas**: Error "Conexión fallida" en index.php, W-N04 no puede alcanzar B-N04

**Diagnóstico**:
```bash
# Desde W-N04, probar conexión:
mysql -h 192.168.140.20 -u bchecker -p

# Ver procesos MySQL activos
mysql -h 192.168.140.20 -u root -p -e "SHOW PROCESSLIST;"

# Revisar logs MySQL
tail -f /var/log/mysql/error.log

# Verificar routing desde W-N04
ip route show
ping 192.168.140.20
```

**Solución**:
- Verificar que bchecker@192.168.40.10 está creado en MySQL
- Comprobar bind-address = 0.0.0.0 en MySQL (/etc/mysql/mysql.conf.d/mysqld.cnf)
- Reiniciar MySQL: `systemctl restart mysql`
- Verificar firewall entre DMZ e Intranet
- Verificar rutas de IP entre R-N04 (debe enrutar DMZ → Intranet)

### 13.5 SFTP usuarios no pueden conectar

**Síntomas**: Conexión denegada, timeout, error de autenticación

**Diagnóstico**:
```bash
# Estado SSH
systemctl status ssh

# Verificar puerto
ss -tlnp | grep 2222

# Usuario existe y está en grupo correcto
id usuario_sftp
getent group sftpusers

# Permisos del home
ls -la /home/usuario_sftp

# Logs de autenticación
tail -f /var/log/auth.log

# Probar conexión
sftp -P 2222 usuario@192.168.40.30
```

**Solución**:
- Usuario debe existir en el sistema y grupo sftpusers
- Shell debe ser /bin/false (sin login SSH)
- Permisos del home: root:root con 755
- Directorio uploads: usuario:sftpusers con 755
- Puerto 2222 debe estar abierto en firewall
- Verificar configuración en /etc/ssh/sshd_config
- Reiniciar servicio: `systemctl restart ssh`

### 13.6 SFTP modo pasivo no funciona

**Síntomas**: Conexión establecida pero no se pueden listar directorios o transferir archivos

**Diagnóstico**:
```bash
# Verificar configuración de puertos pasivos
grep -i passive /etc/ssh/sshd_config

# Verificar firewall
iptables -L -n | grep 40000
iptables -L -n | grep 50000

# Logs del cliente
# Usar cliente SFTP en modo verbose
sftp -vvv -P 2222 usuario@192.168.40.30
```

**Solución**:
- Verificar PassivePorts configurado correctamente
- Abrir rango de puertos 40000-50000 en firewall
- Reiniciar SSH: `systemctl restart ssh`
- En cliente, configurar modo pasivo si es necesario

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
  /etc/ssh/sshd_config (Configuración SSH/SFTP)
  /etc/ssh/ssh_host_* (Claves del servidor)
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

**Si se compromete servidor SFTP**:

1. Detener servicio: `systemctl stop ssh`
2. Restaurar configuración desde backup
3. Regenerar claves del servidor si necesario:
   ```bash
   rm /etc/ssh/ssh_host_*
   dpkg-reconfigure openssh-server
   ```
4. Revisar usuarios y permisos
5. Reiniciar servicio: `systemctl start ssh`

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

# MySQL
mysql -u root -p
SHOW PROCESSLIST;

# Apache
apache2ctl -S            # Virtual hosts
apache2ctl -M            # Módulos cargados
systemctl reload apache2 # Sin desconectar

# SSH/SFTP
sftp -P 2222 usuario@servidor
ssh -p 2222 -v usuario@servidor  # Verbose
tail -f /var/log/auth.log
who
w

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
- **Emergencias**: +34-XXX-XXX-XXXX
- **Horario**: Lunes a Viernes, 8:00 - 20:00 h
- **Escalación**: Coordinador Técnico

---

**Manual de Administración - Infraestructura Multicapa Grupo 04**  
*Documento controlado - Versión 1.1*  
*© 2025 Grupo 04. Todos los derechos reservados.*  
*Acceso restringido a personal administrativo autorizado.*

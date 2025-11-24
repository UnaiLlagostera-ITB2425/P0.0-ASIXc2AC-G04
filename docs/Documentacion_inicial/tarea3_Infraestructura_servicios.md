# Documentación Técnica de Infraestructura y Servicios

## 1. Contexto de la Infraestructura
El sistema se basa en una arquitectura cliente-servidor diseñada para la estabilidad y seguridad.

* **Servidores:** Todos los servidores operan bajo **Sistemas Operativos Linux**, elegidos por su robustez, seguridad nativa y gestión eficiente de recursos (sin interfaz gráfica innecesaria).
* **Clientes:** La red es heterogénea, compuesta por estaciones de trabajo **Linux** y **Windows**. Los servicios seleccionados garantizan compatibilidad total (interoperabilidad) entre ambos sistemas.

---

## 2. Servidor Router (Gateway)
Este servidor Linux centraliza la gestión de la red. Al usar Linux, aprovechamos herramientas nativas del kernel para el enrutamiento.

| Servicio | Software | Justificación Técnica para el Entorno Linux/Híbrido | Alternativa Descartada |
| :--- | :--- | :--- | :--- |
| **DHCP** | `isc-dhcp-server` | Es el estándar en distribuciones Linux. Ofrece compatibilidad universal, entregando IPs correctamente tanto a clientes Windows (que requieren opciones DHCP estándar) como a Linux. | **dnsmasq**: Se descarta en favor de ISC para tener una separación de roles más estricta y profesional en el servidor Linux. |
| **DNS** | `bind9` | El servidor DNS más probado en entornos Unix/Linux. Garantiza que tanto el cliente Windows como el Linux resuelvan los nombres de dominio internos y externos de forma idéntica. | **Windows Server DNS**: Descartado ya que nuestra infraestructura de servidores es 100% Linux, evitando costes de licencias. |
| **NAT / Firewall** | `iptables` | **Integración Nativa.** Al ser un servidor Linux, iptables interactúa directamente con el kernel (Netfilter). Proporciona el rendimiento más alto posible y seguridad a nivel de paquete. | **Firewalls de Hardware**: Se descartan por coste. `iptables` en un servidor Linux bien configurado iguala la seguridad de muchos routers comerciales. |

---

## 3. Servidor Web
Alojado en Linux, aprovechamos el stack **LAMP** (Linux, Apache, MySQL, PHP), la arquitectura web más popular y documentada del mundo.

| Servicio | Software | Justificación Técnica para el Entorno Linux | Alternativa Descartada |
| :--- | :--- | :--- | :--- |
| **Servidor HTTP** | `Apache` | Servidor web por excelencia en Linux. Los clientes (navegadores en Windows o Linux) interactúan de forma transparente. Su gestión de permisos en Linux es granular y segura. | **IIS**: Descartado porque es nativo de Windows Server; no funciona en nuestra infraestructura Linux. |
| **Backend** | `PHP` | Lenguaje nativo del stack LAMP. Se ejecuta con gran eficiencia en Linux y se integra perfectamente con Apache a través de módulos nativos. | **ASP.NET**: Descartado porque requiere un entorno Windows Server para funcionar de manera óptima. |

---

## 4. Servidor de Datos y Base de Datos
Gestión centralizada de archivos e información.

| Servicio | Software | Justificación Técnica para Clientes Mixtos | Alternativa Descartada |
| :--- | :--- | :--- | :--- |
| **Transferencia** | `SFTP` (SSH) | **Seguridad y Compatibilidad.** Al ser servidores Linux, el servicio SSH viene instalado por defecto.<br>• **Cliente Linux:** Conexión nativa.<br>• **Cliente Windows:** Conexión fácil vía WinSCP/FileZilla. | **Samba (SMB)**: Aunque es compatible con Windows, se eligió SFTP porque priorizamos la seguridad (cifrado total) sobre la comodidad de carpetas compartidas. |
| **Base de Datos** | `MySQL` | La "M" del stack LAMP. Es la base de datos relacional más optimizada para correr sobre sistemas de archivos Linux (ext4/xfs). | **SQL Server**: Descartado por ser un producto de Microsoft con costes de licencia y diseñado para Windows. |

---
---

# Detalle y Comparativa de Servicios

Este documento detalla la arquitectura de servicios implementada en nuestros servidores Linux, justificando cada elección técnica mediante una comparación directa con sus competidores más habituales en el mercado.

## 1. Infraestructura Base
* **Servidores:** 100% Linux (Ubuntu).
* **Clientes:** Entorno mixto (1 Estación Linux, 1 Estación Windows).

## 2. Servidor Router (Gateway)
Encargado de la gestión de red, asignación de IPs y seguridad perimetral.

### A. DHCP (Asignación de IPs)
**Servicio elegido:** `isc-dhcp-server`

| Comparativa | ¿Por qué elegimos `isc-dhcp-server`? |
| :--- | :--- |
| **vs. dnsmasq** | `dnsmasq` es ligero y combina DNS/DHCP, ideal para routers domésticos. `isc-dhcp-server` es una solución empresarial dedicada. Lo elegimos porque permite una gestión mucho más estricta de pools y reservas que `dnsmasq` no maneja con tanta solvencia. |
| **vs. Kea DHCP** | `Kea` es el sucesor moderno. Aunque es más rápido, `isc-dhcp-server` tiene décadas de documentación y estabilidad probada. Priorizamos la estabilidad y la cantidad de guías disponibles. |

### B. DNS (Resolución de Nombres)
**Servicio elegido:** `bind9`

| Comparativa | ¿Por qué elegimos `bind9`? |
| :--- | :--- |
| **vs. Unbound** | `Unbound` es excelente como *resolver* (caché), pero `bind9` es el rey de los servidores **autoritativos**. Necesitamos `bind9` para definir nuestras propias zonas locales (`miempresa.lan`) y que todos los clientes las resuelvan correctamente. |
| **vs. Windows DNS** | Al no usar Windows Server, ahorramos el coste de la licencia. `bind9` es el estándar de Internet y ofrece mayor flexibilidad en la configuración de zonas. |

### C. NAT & Firewall
**Servicio elegido:** `iptables`

| Comparativa | ¿Por qué elegimos `iptables`? |
| :--- | :--- |
| **vs. ufw / firewalld** | Son "front-ends" simplificados. Los descartamos porque ocultan la complejidad necesaria para hacer un NAT preciso. Usamos `iptables` puro para tener control absoluto sobre las tablas NAT y FILTER. |
| **vs. nftables** | `nftables` es el sucesor moderno. Sin embargo, la sintaxis de `iptables` sigue siendo el estándar universal. Elegimos `iptables` por madurez y compatibilidad con scripts existentes. |

## 3. Servidor Web
Encargado del alojamiento de aplicaciones.

### A. Servidor HTTP
**Servicio elegido:** `Apache HTTP Server`

| Comparativa | ¿Por qué elegimos `Apache`? |
| :--- | :--- |
| **vs. Nginx** | `Nginx` es más rápido con contenido estático. Elegimos `Apache` por su flexibilidad con los archivos `.htaccess`. Esto permite cambiar la configuración de cada carpeta sin reiniciar el servidor, vital en entornos de desarrollo. |
| **vs. IIS** | IIS es exclusivo de Windows. Al usar servidores Linux, Apache es la opción nativa que ofrece mejor rendimiento y seguridad sin costes. |

### B. Lenguaje Backend
**Servicio elegido:** `PHP`

| Comparativa | ¿Por qué elegimos `PHP`? |
| :--- | :--- |
| **vs. Python (Django)** | Python requiere un servidor de aplicaciones adicional (Gunicorn). `PHP` se comunica directamente con Apache mediante módulos, haciendo el despliegue tan simple como copiar y pegar archivos. |
| **vs. Java (JSP)** | Java requiere compilar código y reiniciar servicios. `PHP` es interpretado al vuelo, lo que acelera los ciclos de desarrollo. |

## 4. Servidor de Datos

### A. Transferencia de Archivos
**Servicio elegido:** `SFTP` (SSH File Transfer Protocol)

| Comparativa | ¿Por qué elegimos `SFTP`? |
| :--- | :--- |
| **vs. FTP** | **FTP es inseguro** (texto plano). SFTP cifra toda la conexión desde el inicio. |
| **vs. Samba (SMB)** | Samba es ideal para redes locales Windows pero "ruidoso" y complejo de asegurar en Internet. SFTP funciona igual de bien en local y remoto usando un solo puerto (22). |

> **Nota para el cliente Windows:** Solo requiere instalar un cliente gratuito como *WinSCP* o *FileZilla*.

### B. Base de Datos
**Servicio elegido:** `MySQL`

| Comparativa | ¿Por qué elegimos `MySQL`? |
| :--- | :--- |
| **vs. PostgreSQL** | Postgres es técnicamente avanzado, pero `MySQL` es más rápido en operaciones de lectura simple (comunes en webs) y es el motor por defecto de la mayoría de CMS. |
| **vs. SQLite** | SQLite no soporta múltiples usuarios escribiendo a la vez eficientemente. Necesitamos la capacidad concurrente de un servidor real como `MySQL`. |

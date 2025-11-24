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
* **Servidores:** 100% Linux (Ubuntu/Debian).
* **Clientes:** Entorno mixto (1 Estación Linux, 1 Estación Windows).

## 2. Servidor Router (Gateway)
Encargado de la gestión de red, asignación de IPs y seguridad perimetral.

### A. DHCP (Asignación de IPs)
**Servicio elegido:** `isc-dhcp-server`

| Comparativa | ¿Por qué elegimos `isc-dhcp-server`? |
| :--- | :--- |
| **vs. dnsmasq** | (Descartado en tabla principal). `dnsmasq` es ligero y combina DNS/DHCP, ideal para routers domésticos. `isc-dhcp-server` es una solución empresarial dedicada. Lo elegimos porque permite una gestión mucho más estricta de pools y reservas. |
| **vs. Kea DHCP** | `Kea` es el sucesor moderno. Aunque es más rápido, `isc-dhcp-server` tiene décadas de documentación y estabilidad probada. Priorizamos la estabilidad y la cantidad de guías disponibles. |

### B. DNS (Resolución de Nombres)
**Servicio elegido:** `bind9`

| Comparativa | ¿Por qué elegimos `bind9`? |
| :--- | :--- |
| **vs. Windows DNS** | (Descartado en tabla principal). Al no usar Windows Server, ahorramos el coste de la licencia. `bind9` es el estándar de Internet, nativo de Linux y ofrece mayor flexibilidad en la configuración de zonas. |
| **vs. Unbound** | `Unbound` es excelente como *resolver* (caché), pero `bind9` es superior como servidor **autoritativo**. Necesitamos `bind9` para definir y controlar nuestras propias zonas locales (`miempresa.lan`). |

### C. NAT & Firewall
**Servicio elegido:** `iptables`

| Comparativa | ¿Por qué elegimos `iptables`? |
| :--- | :--- |
| **vs. Firewalls de Hardware** | (Descartado en tabla principal). Dispositivos como Cisco o Fortinet son excelentes pero costosos. `iptables` ofrece capacidades de firewall de nivel empresarial integradas gratuitamente en el kernel de Linux. |
| **vs. ufw / firewalld** | Son "interfaces simplificadas". Los descartamos porque ocultan la complejidad necesaria para hacer un NAT preciso. Usamos `iptables` puro para tener control absoluto y directo sobre el tráfico. |

## 3. Servidor Web
Encargado del alojamiento de aplicaciones.

### A. Servidor HTTP
**Servicio elegido:** `Apache HTTP Server`

| Comparativa | ¿Por qué elegimos `Apache`? |
| :--- | :--- |
| **vs. IIS** | (Descartado en tabla principal). IIS es exclusivo de Windows. Al usar servidores Linux, Apache es la opción nativa que ofrece mejor rendimiento, seguridad y compatibilidad con el stack LAMP sin costes de licencia. |
| **vs. Nginx** | `Nginx` es más rápido con contenido estático. Elegimos `Apache` por su compatibilidad con archivos `.htaccess`, lo que permite configurar redirecciones y permisos por carpeta de forma dinámica y sencilla. |

### B. Lenguaje Backend
**Servicio elegido:** `PHP`

| Comparativa | ¿Por qué elegimos `PHP`? |
| :--- | :--- |
| **vs. ASP.NET** | (Descartado en tabla principal). Tecnología de Microsoft. Requiere servidores Windows o configuraciones complejas en Linux (.NET Core). `PHP` es nativo de Linux y se integra de forma inmediata con Apache. |
| **vs. Python (Django)** | Python requiere servidores de aplicaciones adicionales (Gunicorn). `PHP` se comunica directamente con Apache mediante módulos, haciendo el despliegue tan simple como copiar y pegar archivos. |

## 4. Servidor de Datos

### A. Transferencia de Archivos
**Servicio elegido:** `SFTP` (SSH File Transfer Protocol)

| Comparativa | ¿Por qué elegimos `SFTP`? |
| :--- | :--- |
| **vs. Samba (SMB)** | (Descartado en tabla principal). Samba es ideal para compartir carpetas en LAN Windows, pero es inseguro exponerlo a Internet. `SFTP` es seguro por diseño (cifrado) y funciona perfectamente tanto en local como en remoto. |
| **vs. FTP** | **FTP es inseguro** ya que transmite contraseñas en texto plano. `SFTP` utiliza el protocolo SSH, garantizando que todos los datos viajan encriptados, cumpliendo estándares de seguridad actuales. |

### B. Base de Datos
**Servicio elegido:** `MySQL`

| Comparativa | ¿Por qué elegimos `MySQL`? |
| :--- | :--- |
| **vs. SQL Server** | (Descartado en tabla principal). Producto de Microsoft costoso y optimizado para Windows. `MySQL` es Open Source, nativo de Linux y el estándar de la industria para aplicaciones web. |
| **vs. PostgreSQL** | Postgres es técnicamente avanzado en estándares SQL, pero `MySQL` suele ser más rápido en operaciones de lectura simple (comunes en webs) y tiene una curva de aprendizaje más suave para administración básica. |

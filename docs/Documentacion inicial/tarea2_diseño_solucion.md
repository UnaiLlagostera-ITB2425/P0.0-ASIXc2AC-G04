<div style="text-align: center;">
  <h1>Informe de Diseño de Infraestructura de Red (Con Servidor Linux)</h1>
</div>

## 1. Resumen General
Se desplegará una infraestructura con un total de 4 servidores que cumplirán múltiples funciones para soportar los servicios de la organización. La red está dividida en dos subredes principales para mejorar la seguridad y el rendimiento:

- **Nube de servidores:** 192.168.40.0/24  
- **Nube de clientes:** 192.168.140.0/24

---

## 2. Equipos y Funciones

| Hostname | Función(s)                         | IP                  | Descripción                                                       |
|----------|----------------------------------|---------------------|------------------------------------------------------------------|
| R-N04    | Router, DHCP, DNS                | 192.168.40.1        | Controla el tráfico entre subredes, asigna IPs dinámicas y resuelve nombres.|
| W-N04    | Servidor Web                     | 192.168.40.10        | Aloja el sitio web y servicios web internos o externos.          |
| B-N04    | Servidor Base de datos MySQL     | 192.168.40.20        | Base de datos.       |
| F-N04    | Servidor FTP                    | 192.168.40.30        | Servicio FTP para transferencia de archivos.                    |

---

## 3. Diseño de Red

### Subred Servidores (192.168.40.0/24)
- Segmento dedicado para los servidores principales.
- IPs fijas asignadas para garantizar accesibilidad y gestión sencilla.
- Interfaces de esas máquinas configuradas con puertas de enlace hacia el router R-N04.

### Subred Clientes (192.168.140.0/24)
- Segmento para los dispositivos cliente y usuarios finales.
- Las IPs pueden ser asignadas por DHCP desde el router R-N04.
- Comunicaciones hacia servidores reguladas por reglas de firewall y NAT en el router.

<div align="center">
  <img src="/media/DISEÑO RED.drawio.png" alt="Diseno de Red">
</div>

---

## 4. Servicios por Servidor

### R-N04 (Router, DHCP, DNS)
- Router encargado del enrutamiento entre subredes DMZ, NAT y Intranet.
- DHCP configurado para asignar IPs en la red de clientes.
- DNS configurado para resolucion de nombres, incluyendo R-N04 y servidores asociados.

### W-N04 (Servidor Web)
- Proveen servicio HTTPS.
- Alojamiento de la página web institucional y aplicaciones web.

### B-N04 (Servidor Base de datos)
- Servidor MySQL para la gestión y almacenamiento de datos.
- Usuario: `bchecker`
- Contraseña: `bchecker121`
- Almacenamiento y carga de la base de datos externa (por ejemplo CSV de equipamientos educativos).

### F-N04 (Servidor FTPS)
- Servicio FTPS para transferencia segura de archivos.
- Punto central para intercambio de información y backups.

---

## 5. Servicios que proveerá el Router R-N04

### DMZ (Zona Desmilitarizada)
- Se configura una subred dedicada para alojar servidores accesibles desde internet, como el servidor web (W-N04) o el servidor FTPS (F-N04).
- El router R-N04 segmenta esta red para aislarla de la red interna (Intranet) mediante listas de control de acceso (ACL) y firewall.
- El tráfico externo autorizado hacia la DMZ se permite para servicios específicos, garantizando seguridad.

### Intranet
- La Intranet es la red interna privada donde se ubican los dispositivos y usuarios.
- El acceso a la DMZ y a recursos externos (internet) está controlado estrictamente, permitiendo sólo el tráfico necesario.

### NAT (Network Address Translation)
- R-N04 actúa como dispositivo NAT para permitir que los dispositivos de la red interna (Intranet) accedan a internet usando una única IP pública.
- Se implementa NAT para controlar el acceso a las redes privadas desde o hacia la DMZ, traduciendo direcciones IP y puertos para mejorar la seguridad.

---

## 6. Control de acceso y seguridad adicional con Linux Server

- Los clientes ubicados en la subred 192.168.140.0/24 **no tienen acceso directamente a la DMZ** para evitar riesgos de seguridad.
- El router R-N04 implementará reglas de firewall para bloquear el tráfico directo desde la red de clientes hacia la DMZ, permitiendo sólo conexiones estrictamente necesarias.
- Los servidores Linux pueden usar herramientas robustas como `iptables`, `firewalld` o `ufw` para crear políticas avanzadas de control de acceso y protección local.

---

## 7. Notas Adicionales
- Se recomienda implementar monitorización continua y backups regulares.
- La infraestructura puede estar basada en hardware físico o virtualización según recursos.
- Se debe gestionar adecuadamente el ciclo de vida y gestión de incidentes para la red y servicios.

---
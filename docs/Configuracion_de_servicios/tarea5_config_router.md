# Configuración del Servidor NAT y DHCP (R-N04)

Este documento describe el proceso de configuración en el router **R-N04** para proporcionar servicios de enrutamiento, NAT y DHCP en una red corporativa. El despliegue avanzado de DNS y otros servicios se documenta en manuales separados.

---

## 1. Introducción

El router R-N04 es el núcleo de la infraestructura de red interna, encargado de distribuir tráfico entre las distintas redes, permitir el acceso a internet mediante NAT y gestionar la asignación dinámica de direcciones IP a los clientes a través del servicio DHCP.

---

## 2. Requisitos previos

Antes de comenzar la configuración asegúrate de tener:

- Ubuntu Server correctamente instalado y actualizado.
- Interfaces de red físicas configuradas (ejemplo: WAN, LAN, DMZ).
- Acceso administrativo al sistema (usuario con permisos sudo).

---

## 3. Configuración de interfaces de red

Revisa y ajusta la configuración de red (Netplan o similar) para definir:

- **WAN:** acceso a internet vía DHCP (ej. enp1s0).
- **LAN:** red interna con IP estática (ej. enp2s0, IP: 192.168.40.1).
- **DMZ/Clientes:** otras interfaces según el diseño de red.

Verifica conectividad y rutas locales entre las interfaces.

---

## 4. Habilitar NAT (Masquerade) en el router

### 4.1 Activar el reenvío de paquetes

En `/etc/sysctl.conf` asegúrate de habilitar:

```bash
net.ipv4.ip_forward=1
```

<div align="center">
  <img src="/media/ip_forward_conf.png" alt="Vista de Configuracion de Forwarding">
</div>

Aplica los cambios:

```bash
sudo sysctl -p
```

### 4.2 Configurar las reglas NAT

Utiliza iptables para permitir que los clientes de la LAN accedan a Internet mediante NAT:

```bash
sudo iptables -t nat -A POSTROUTING -o enp1s0 -j MASQUERADE
sudo iptables -A FORWARD -i enp2s0 -o enp1s0 -j ACCEPT
sudo iptables -A FORWARD -i enp1s0 -o enp2s0 -m state --state RELATED,ESTABLISHED -j ACCEPT
```

<div align="center">
  <img src="/media/nat_config.png" alt="Comandos de NAT">
</div>

Guarda las reglas persistentes:

```bash
sudo apt install iptables-persistent -y
sudo netfilter-persistent save
```

---

## 5. Configuración del servicio DHCP

### 5.1 Instalación de DHCP

Instala el servidor DHCP:

```bash
sudo apt install isc-dhcp-server -y
```

### 5.2 Configuración del ámbito DHCP

Edita `/etc/dhcp/dhcpd.conf` y define el rango de direcciones, gateway y DNS para clientes:

```bash
subnet 192.168.140.0 netmask 255.255.255.0 {  
  range 192.168.140.10 192.168.140.50;
  option routers 192.168.140.1;
  option domain-name-servers 192.168.40.1, 8.8.8.8;
  option domain-name "grup4.com";
  default-lease-time 600;
  max-lease-time 7200;
  }
```

Configura la interfaz de red del DHCP en `/etc/default/isc-dhcp-server`:

```bash
INTERFACESv4="enp3s0"
```

<div align="center">
  <img src="/media/dhcp_config_capture.png" alt="Configuracion DHCP">
</div>

<div align="center">
  <img src="/media/dhcp_serve_port_capture.png" alt="Puerto servicio DHCP">
</div>

Reinicia el servicio para aplicar cambios:

```bash
sudo systemctl restart isc-dhcp-server
```

<div align="center">
  <img src="/media/dhcp_status_capture.png" alt="DHCP status">
</div>

---

## 6. Verificación de la configuración

- Prueba NAT verificando acceso a Internet desde clientes conectados a la LAN.
- Prueba DHCP comprobando que clientes reciben IP, gateway y DNS esperados.
- Revisa conectividad entre las subredes (LAN, DMZ, WAN).

---

## 7. Buenas prácticas y consideraciones de seguridad

- Realiza copias de los archivos de configuración modificados.
- Aplica reglas de firewall estrictas para limitar tráfico entre DMZ y LAN.
- Documenta incidencias y cambios en el repositorio del proyecto.

---



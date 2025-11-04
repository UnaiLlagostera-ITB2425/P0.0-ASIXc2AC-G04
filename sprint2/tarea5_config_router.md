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


# Manual de Despliegue Inicial del Router

Este documento describe los pasos necesarios para el despliegue inicial y configuración base de un equipo router con sistema operativo **Ubuntu Server**, previo a la instalación y configuración de los servicios **DHCP**, **DNS** y **NAT**.

---

## 1. Objetivo

El objetivo del procedimiento es preparar una máquina con **Ubuntu Server** para funcionar como router básico entre diferentes redes. Este despliegue dejará operativo el sistema con las interfaces configuradas y verificadas, asegurando conectividad IP antes de instalar y configurar otros servicios de red.

---

## 2. Requerimientos previos

### 2.1 Hardware

- Equipo con al menos **2 interfaces de red**:
  - **enp1s0**: Interfaz WAN (acceso a Internet).
  - **enp2s0**: Interfaz LAN (red interna 192.168.40.0/24).
  - (Opcional) **enp3s0**: interfaz DMZ o redes adicionales.
- Memoria RAM mínima: 1 GB.
- Espacio en disco: 10 GB disponibles.

### 2.2 Software

- Ubuntu Server 22.04 o superior.
- Acceso con usuario con privilegios de **sudo**.
- Conexión temporal a Internet para instalar paquetes.

---

## 3. Configuración inicial del sistema

### 3.1 Actualización del sistema

Antes de iniciar cualquier configuración, actualiza los paquetes del sistema:


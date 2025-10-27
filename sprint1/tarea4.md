# Despliegue Inicial del Servidor DNS - Parte del Servidor R-N04

Este documento describe la configuración y despliegue inicial del servicio DNS, que forma parte integral del servidor **R-N04**. Para evitar repetición, no se incluyen aquí detalles de instalación y configuración base del servidor físico o sistema operativo que ya están documentados en el manual general del servidor R-N04.

---

## 1. Introducción

El servicio DNS es un componente crítico que permite resolver nombres de dominio internos en direcciones IP, facilitando la gestión y acceso a los recursos de red. Este manual detalla las etapas iniciales necesarias para el despliegue del servicio DNS, asegurando su correcta instalación y preparación para las configuraciones avanzadas posteriores.

---

El servidor R-N04, con su sistema operativo y hardware preconfigurados, debe contar con conectividad de red estable, capacidad de acceso remoto seguro y respaldo configurado antes de iniciar la instalación del servidor DNS.

Se recomienda realizar las siguientes acciones previas al despliegue del servicio DNS:

- Verificación del estado general del sistema y actualizaciones pendientes del sistema operativo.
- Comprobación de conectividad de red y accesos al servidor desde la ubicación de administración.
- Configuración preliminar de usuarios y roles para la administración del servidor.
- Aseguramiento del entorno para instalación mediante actualizaciones y repositorios configurados.

---

## 2. Instalación del Servicio DNS

Para el despliegue inicial del DNS en el servidor R-N04, se instalarán los paquetes principales del software Bind9, junto con las utilidades y documentación asociadas que facilitan la administración del servicio y futuras configuraciones.

Comandos necesarios para instalar el servicio:

```bash
sudo apt update
sudo apt install bind9 bind9utils bind9-doc -y
```


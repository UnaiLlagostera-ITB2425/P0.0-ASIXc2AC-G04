# Despliegue Inicial del Servidor DNS

Este documento describe la configuración y despliegue inicial del servicio DNS, que forma parte integral del servidor **R-N04**. Para evitar repetición, no se incluyen aquí detalles de instalación y configuración base del servidor físico o sistema operativo que ya están documentados en el manual general del servidor R-N04.

---

## 1. Introducción

El servicio DNS es un componente crítico que permite resolver nombres de dominio internos en direcciones IP, facilitando la gestión y acceso a los recursos de red. Este manual detalla las etapas iniciales necesarias para el despliegue del servicio DNS, asegurando su correcta instalación y preparación para las configuraciones avanzadas posteriores.

<div align="center">
  <img src="/media/dns_how_works.gif" alt="Como funciona el DNS">
</div>

<div align="center">
<a href="https://igulms.iqdigit.com/storage/15584/Domain-Name-System-(DNS)-in-Application-Layer---GeeksforGeeks.pdf">Fuente: Domain Name System (DNS) in Application Layer</a>
</div>

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

Bind9 es el software de referencia en sistemas Linux para implementar servidores DNS autoritativos y de caché. Ofrece robustez, flexibilidad y soporte completo para configuraciones avanzadas: zonas externas, internas, vistas separadas y controles de acceso. 

Utilizar Bind9 permite estructurar la resolución de nombres según las necesidades de la organización, soportar servicios críticos y garantizar rendimiento y escalabilidad.

Comandos necesarios para instalar el servicio:

```bash
sudo apt update
sudo apt install bind9 bind9utils bind9-doc -y
```

# Manual de Usuarios

**Versión:** 1.0  
**Fecha:** Noviembre 2025  
**Destinatarios:** Usuarios finales (clientes)

---

## Índice

1. [Introducción](#1-introducción)
2. [Requisitos del Sistema](#2-requisitos-del-sistema)
3. [Arquitectura del Sistema](#3-arquitectura-del-sistema)
4. [Servicios Disponibles](#4-servicios-disponibles)
5. [Acceso al Portal Web](#5-acceso-al-portal-web)
   - 5.1 [¿Qué es el Portal Web?](#51-qué-es-el-portal-web)
   - 5.2 [Cómo acceder](#52-cómo-acceder)
   - 5.3 [Navegación por el Portal](#53-navegación-por-el-portal)
   - 5.4 [Consulta de Equipamientos Educativos](#54-consulta-de-equipamientos-educativos)
6. [Acceso al Servidor FTP](#6-acceso-al-servidor-ftp)
   - 6.1 [¿Qué es el Servidor FTP?](#61-qué-es-el-servidor-ftp)
   - 6.2 [Clientes FTP Recomendados](#62-clientes-ftp-recomendados)
   - 6.3 [Instalación de FileZilla](#63-instalación-de-filezilla)
   - 6.4 [Conexión al Servidor FTP](#64-conexión-al-servidor-ftp)
   - 6.5 [Subir Archivos](#65-subir-archivos)
   - 6.6 [Descargar Archivos](#66-descargar-archivos)
7. [Preguntas Frecuentes (FAQ)](#7-preguntas-frecuentes-faq)
8. [Solución de Problemas Comunes](#8-solución-de-problemas-comunes)
9. [Información de Contacto](#9-información-de-contacto)

---

## 1. Introducción

Bienvenido al sistema de Infraestructura Multicapa del Grupo 04. Este manual está diseñado para ayudarle a utilizar los servicios disponibles de forma sencilla y eficaz.

El sistema proporciona dos servicios principales:

- **Portal Web**: Para consultar información sobre equipamientos educativos.
- **Servidor FTP**: Para transferir archivos de forma segura.

Este documento le guiará paso a paso en el uso de ambos servicios.

---

## 2. Requisitos del Sistema

Para utilizar los servicios del sistema, necesita:

### Requisitos Mínimos

- **Conexión a Internet**: Conexión estable a la red local.
- **Navegador Web**: Cualquier navegador moderno actualizado (Chrome, Firefox, Edge, Safari).
- **Cliente FTP** *(solo para acceso a FTP)*: Se recomienda FileZilla (gratuito).

### Credenciales de Acceso

- **Portal Web**: No requiere autenticación, acceso público.
- **Servidor FTP**: Requiere usuario y contraseña proporcionados por el administrador del sistema.

---

## 3. Arquitectura del Sistema

### Infraestructura de Red

El sistema está construido sobre una infraestructura de red robusta y segura:

- **Asignación de Direcciones**: La red utiliza un servicio **DHCP** que le asigna automáticamente una dirección IP cuando se conecta. Esto significa que no tiene que preocuparse por configurar manualmente su conexión.

- **Base de Datos Segura**: Toda la información que consulta en el portal web está almacenada en una **base de datos protegida y cifrada**. Sus datos están seguros en todo momento.

- **Conexiones Cifradas**: Todas las comunicaciones entre su dispositivo y nuestros servicios están protegidas mediante **cifrado SSL/TLS**, garantizando la privacidad y seguridad de sus datos.

> **En resumen**: El sistema está diseñado pensando en su seguridad. Puede usar los servicios con confianza.

---

## 4. Servicios Disponibles

El sistema ofrece los siguientes servicios:

| Servicio | Dirección de Acceso | Descripción |
|----------|---------------------|-------------|
| **Portal Web** | `www.grup4.com` | Consulta de equipamientos educativos de Barcelona |
| **Servidor FTP** | `ftp.grup4.com` | Transferencia segura de archivos (subida y descarga) |

---

## 5. Acceso al Portal Web

### 5.1 ¿Qué es el Portal Web?

El Portal Web es una aplicación que permite consultar información detallada sobre equipamientos educativos de Barcelona. Los datos incluyen:

- Nombre del equipamiento
- Dirección completa
- Tipo de institución
- Barrio y distrito
- Coordenadas geográficas
- Horarios

Toda esta información se mantiene en una **base de datos segura** que se actualiza regularmente.

### 5.2 Cómo acceder

**Paso 1:** Abra su navegador web preferido.

**Paso 2:** En la barra de direcciones, escriba:

```
www.grup4.com
```

**Paso 3:** Presione la tecla **Enter**.

La página principal del portal se cargará automáticamente.

### 5.3 Navegación por el Portal

Una vez dentro del portal, verá la interfaz principal con la información de los equipamientos educativos organizados en formato tabla.

**Elementos de la interfaz:**

- **Encabezado**: Título del portal "Grupo 04 – Infraestructura Multicapa"
- **Tabla de datos**: Listado completo de equipamientos educativos
- **Columnas disponibles**: Nombre, Institución, Dirección, Barrio, Distrito, Código Postal

### 5.4 Consulta de Equipamientos Educativos

La información se presenta de manera clara y organizada. Puede:

- **Visualizar todos los registros**: Desplácese hacia abajo para ver más equipamientos.
- **Leer información detallada**: Cada fila muestra datos completos de un equipamiento.

> **Nota**: El portal está diseñado únicamente para consulta de información. No es posible modificar o agregar datos desde esta interfaz.

---

## 6. Acceso al Servidor FTP

### 6.1 ¿Qué es el Servidor FTP?

El servidor FTP (File Transfer Protocol) le permite transferir archivos entre su ordenador y el servidor de forma segura. Las conexiones están cifradas mediante **FTPS** (FTP Seguro) para proteger sus datos.

**Usos principales:**

- Subir archivos al servidor
- Descargar archivos desde el servidor
- Gestionar sus archivos personales

### 6.2 Clientes FTP Recomendados

Para conectarse al servidor FTP necesita un programa cliente. Recomendamos:

| Cliente | Sistema Operativo | Descarga |
|---------|-------------------|----------|
| **FileZilla** (Recomendado) | Windows, macOS, Linux | [filezilla-project.org](https://filezilla-project.org) |
| **WinSCP** | Windows | [winscp.net](https://winscp.net) |
| **Cyberduck** | Windows, macOS | [cyberduck.io](https://cyberduck.io) |

> **Recomendación**: FileZilla es gratuito, fácil de usar y compatible con todos los sistemas operativos.

### 6.3 Instalación de FileZilla

**Paso 1:** Visite la página oficial: [https://filezilla-project.org](https://filezilla-project.org)

**Paso 2:** Descargue **FileZilla Client** (no FileZilla Server).

**Paso 3:** Ejecute el instalador y siga las instrucciones en pantalla.

**Paso 4:** Una vez instalado, abra FileZilla desde el menú de aplicaciones.

### 6.4 Conexión al Servidor FTP

Una vez que tenga FileZilla instalado y sus credenciales de acceso, siga estos pasos:

**Paso 1:** Abra FileZilla.

**Paso 2:** En la parte superior de la ventana, complete los siguientes campos:

- **Servidor**: `ftp.grup4.com`
- **Nombre de usuario**: *Su usuario proporcionado por el administrador*
- **Contraseña**: *Su contraseña proporcionada por el administrador*
- **Puerto**: `21`

**Paso 3:** Haga clic en el botón **"Conexión rápida"**.

**Paso 4:** Si es su primera conexión, aparecerá una ventana sobre el certificado SSL:

- Marque la casilla **"Confiar siempre en este certificado en futuras sesiones"**
- Haga clic en **"Aceptar"**

**Paso 5:** Una vez conectado correctamente, verá:

- **Panel izquierdo**: Archivos de su ordenador (local)
- **Panel derecho**: Archivos del servidor (remoto)

> **Importante**: Mantenga sus credenciales en un lugar seguro. No las comparta con otras personas.

### 6.5 Subir Archivos

Para subir archivos desde su ordenador al servidor:

**Paso 1:** En el **panel izquierdo** (local), navegue hasta la carpeta donde están los archivos que desea subir.

**Paso 2:** Seleccione el archivo o carpeta que desea subir.

**Paso 3:** Arrastre el archivo hacia el **panel derecho** (servidor).

También puede hacer clic derecho sobre el archivo y seleccionar **"Subir"**.

**Paso 4:** El archivo se transferirá automáticamente. Verá el progreso en la parte inferior de la ventana.

### 6.6 Descargar Archivos

Para descargar archivos desde el servidor a su ordenador:

**Paso 1:** En el **panel derecho** (servidor), navegue hasta la carpeta donde está el archivo que desea descargar.

**Paso 2:** Seleccione el archivo o carpeta que desea descargar.

**Paso 3:** Arrastre el archivo hacia el **panel izquierdo** (local) en la ubicación donde desea guardarlo.

También puede hacer clic derecho sobre el archivo y seleccionar **"Descargar"**.

**Paso 4:** El archivo se descargará automáticamente a su ordenador.

---

## 7. Preguntas Frecuentes (FAQ)

### ¿Necesito registrarme para usar el portal web?

No, el portal web es de acceso público y no requiere registro ni autenticación.

### ¿Puedo modificar la información del portal web?

No, el portal está diseñado únicamente para consulta. Solo los administradores pueden modificar la información.

### ¿Cómo obtengo credenciales para el servidor FTP?

Las credenciales de acceso FTP son proporcionadas por el administrador del sistema. Contacte con soporte si aún no las ha recibido.

### ¿Es seguro el servidor FTP?

Sí, el servidor utiliza **FTPS** (FTP Seguro) con cifrado SSL/TLS para proteger sus datos durante la transferencia.

### ¿Cómo recibo automáticamente mi dirección de red?

La red utiliza un servicio **DHCP** que asigna automáticamente direcciones IP a todos los dispositivos conectados. No necesita hacer nada especial; simplemente conéctese y el sistema le asignará una dirección automáticamente.

### ¿Dónde se guardan los datos del portal?

Todos los datos que consulta en el portal web están almacenados en una **base de datos protegida y cifrada**. Sus datos están seguros en todo momento gracias a nuestras medidas de seguridad.

### ¿Puedo acceder al FTP desde mi navegador web?

No es recomendable. Debe utilizar un cliente FTP como FileZilla para aprovechar todas las funcionalidades y seguridad del sistema.

### ¿Hay límite de tamaño para los archivos que puedo subir?

Consulte con el administrador del sistema sobre las políticas de uso y límites de almacenamiento.

### ¿Puedo compartir mi carpeta FTP con otros usuarios?

Cada usuario tiene acceso únicamente a su propia carpeta. No debe compartir sus credenciales con otras personas.

---

## 8. Solución de Problemas Comunes

### No puedo acceder al portal web

**Problema**: El navegador muestra un error o no carga la página.

**Soluciones**:
- Verifique que escribió correctamente la dirección: `www.grup4.com`
- Compruebe su conexión a Internet
- Intente con otro navegador web
- Limpie la caché y cookies de su navegador
- Contacte con el administrador del sistema

### No puedo conectarme al servidor FTP

**Problema**: FileZilla muestra un error de conexión.

**Soluciones**:
- Verifique que sus credenciales (usuario y contraseña) sean correctas
- Compruebe que el servidor sea: `ftp.grup4.com`
- Asegúrese de que el puerto sea: `21`
- Verifique su conexión a Internet
- Desactive temporalmente el firewall o antivirus para comprobar si está bloqueando la conexión
- Contacte con el administrador del sistema

### El certificado SSL del FTP no es confiable

**Problema**: Aparece una advertencia sobre el certificado SSL.

**Solución**:
- Esto es normal en la primera conexión
- Marque la opción **"Confiar siempre en este certificado"**
- Haga clic en **"Aceptar"** para continuar

### La transferencia de archivos es muy lenta

**Problema**: Los archivos tardan mucho en subirse o descargarse.

**Soluciones**:
- Compruebe la velocidad de su conexión a Internet
- Evite transferir archivos muy grandes en horas punta
- Cierre otros programas que estén usando la conexión
- Intente más tarde si el servidor está saturado

### Se desconecta automáticamente del FTP

**Problema**: FileZilla se desconecta después de unos minutos sin actividad.

**Solución**:
- Esto es una medida de seguridad automática del servidor
- Simplemente vuelva a conectarse haciendo clic en **"Conexión rápida"**
- En FileZilla, vaya a **Editar > Ajustes > Conexión** y configure el tiempo de espera

---

## 9. Información de Contacto

Si tiene alguna duda, problema técnico o necesita asistencia adicional, contacte con el equipo de soporte:

**Equipo de Soporte Técnico - Grupo 04**

- **Correo electrónico**: soporte@grup4.com
- **Horario de atención**: Lunes a Viernes, 9:00 - 18:00 h

Al contactar con soporte, incluya la siguiente información:

- Descripción detallada del problema
- Capturas de pantalla (si es posible)
- Servicio afectado (Portal Web o FTP)
- Mensajes de error que aparezcan

---

## Notas Finales

Este manual ha sido diseñado para facilitar el uso de los servicios del sistema. Si tiene sugerencias para mejorar esta documentación, no dude en comunicárnoslas.

**Gracias por utilizar nuestros servicios.**

---

**Manual de Usuarios - Infraestructura Multicapa Grupo 04**  
*Documento controlado - Versión 1.0*  
*© 2025 Grupo 04. Todos los derechos reservados.*

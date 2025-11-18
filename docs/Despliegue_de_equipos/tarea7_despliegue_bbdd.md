# Documentación de despliegue inicial del servidor de Base de Datos (B-N04)

## Instalación de MySQL Server

Para el despliegue del servidor de base de datos en la máquina B-N04, se ha utilizado el paquete oficial de MySQL Server siguiendo la estructura definida por el proyecto.  
Comando utilizado para la instalación:
```sql
sudo apt install mysql-server
```
Esto instala el servidor MySQL y todas las dependencias necesarias sobre el sistema operativo Linux, tal como muestra la evidencia:

![Instalación MySQL Server](/media/cano_instalacion_MYSQL.png)

## Comprobación del estado del servicio

Una vez finalizada la instalación, se ha verificado que el servicio MySQL esté en ejecución y operativo utilizando:
```sql
sudo systemctl status mysql
```
La comprobación muestra que el servidor está activo y listo para aceptar conexiones:

![Estado MySQL activo](/media/cano_statusBD.png)

## Estado actual

- El servidor MySQL está correctamente instalado en el equipo B-N04.
- No se han realizado todavía tareas de configuración, creación de usuarios o bases de datos.
- El servicio está listo para proceder con la configuración de acceso remoto y preparación de las bases de datos y usuarios según los requisitos de los siguientes sprints.

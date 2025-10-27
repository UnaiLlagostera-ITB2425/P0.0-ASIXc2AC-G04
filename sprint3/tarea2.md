# Tarea 2: Desarrollo de la aplicación de consulta de datos

## Configuración para conexión remota con la BBDD desde s. WEB (192.168.40.10)

### Archivo `db.php` para conexión a la base de datos MySQL

Este archivo establece la conexión entre PHP y MySQL usando MySQLi, manejando credenciales y verificando errores para garantizar comunicación estable con la base de datos.

#### Código Completo

```php
<?php
$host = '192.168.40.20';
$user = 'bchecker';
$pass = 'bchecker121';
$db   = 'crud_db';

$conn = new mysqli($host, $user, $pass, $db);

if ($conn->connect_error) {
    die("Conexión fallida: " . $conn->connect_error);
}

$conn->set_charset("utf8");
?>
```

***

#### Explicación por Secciones

##### 1. Variables de Configuración
```php
$host = '192.168.40.20';
$user = 'bchecker';
$pass = 'bchecker121';
$db   = 'crud_db';
```

**Propósito**: Define los parámetros de conexión a la base de datos MySQL.

- **$host**: Dirección IP del servidor MySQL (192.168.40.20 - servidor BBDD/SSH)
- **$user**: Nombre de usuario de MySQL
- **$pass**: Contraseña del usuario
- **$db**: Nombre de la base de datos a utilizar

##### 2. Creación de Conexión
```php
$conn = new mysqli($host, $user, $pass, $db);
```

**Propósito**: Crea un objeto de conexión MySQLi.

- Establece conexión con el servidor MySQL remoto
- Utiliza la extensión MySQLi (MySQL Improved)
- El objeto `$conn` se usará en `index.php` para ejecutar consultas


##### 3. Manejo de Errores
```php
if ($conn->connect_error) {
    die("Conexión fallida: " . $conn->connect_error);
}
```

**Propósito**: Verifica que la conexión sea exitosa.

- **$conn->connect_error**: Contiene el mensaje de error si la conexión falla
- **die()**: Detiene la ejecución del script y muestra el mensaje de error
- Útil para detectar problemas de:
  - Host inaccesible
  - Credenciales incorrectas
  - Base de datos inexistente
  - Permisos insuficientes


##### 4. Configuración de Codificación
```php
$conn->set_charset("utf8");
```

**Propósito**: Establece la codificación de caracteres UTF-8.

- Garantiza que los datos con acentos y caracteres especiales se muestren correctamente
- Previene problemas de codificación entre la BD y la aplicación web
- **Importante** para contenido en español (acentos, ñ, etc.)

***

### Configuración MySQL para Acceso Remoto

#### 1. Verificar la Base de Datos

```sql
SHOW DATABASES;
USE crud_db;
SHOW TABLES;
DESCRIBE equipaments_educacio;
```

**Propósito**: Confirmar que existe la base de datos `crud_db` y la tabla `equipaments_educacio`.

***

#### 2. Crear Usuario con Acceso Remoto

```sql
CREATE USER 'bchecker'@'192.168.40.10' IDENTIFIED BY 'bchecker121';
```

**Propósito**: Permite que `bchecker` se conecte desde la IP del servidor web (192.168.40.10)

***

#### 3. Otorgar Permisos sobre la Base de Datos

```sql
GRANT ALL PRIVILEGES ON crud_db.* TO 'bchecker'@'192.168.40.10';
FLUSH PRIVILEGES;
```

**Propósito**: 
- **GRANT**: Da todos los permisos (SELECT, INSERT, UPDATE, DELETE, etc.) sobre `crud_db`
- **FLUSH**: Aplica los cambios inmediatamente

***

#### 4. Verificar Permisos

```sql
SHOW GRANTS FOR 'bchecker'@'192.168.40.10';
```

**Resultado esperado**:
```
GRANT ALL PRIVILEGES ON `crud_db`.* TO `bchecker`@`192.168.40.10`
```

***

#### 5. Configurar MySQL para Escuchar en la Red

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

**Cambiar**:
```ini
# Antes:
bind-address = 127.0.0.1

# Después:
bind-address = 0.0.0.0
```

**Propósito**: Permite conexiones desde cualquier IP (no solo localhost)

Guardar: `Ctrl+O`, `Enter`, `Ctrl+X`

***

#### 6. Reiniciar MySQL

```bash
sudo systemctl restart mysql
```

**Propósito**: Aplica la configuración del `bind-address`

![web_ddbb](/media/comprovació_DDBB_web.png)

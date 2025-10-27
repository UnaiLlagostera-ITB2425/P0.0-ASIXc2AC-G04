## Archivo `db.php` para conexión a la base de datos MySQL

Este archivo establece la conexión entre PHP y MySQL usando MySQLi, manejando credenciales y verificando errores para garantizar comunicación estable con la base de datos.

```php
<?php
$host = '192.168.140.10';
$user = 'bchecker';
$pass = 'bchecker121';
$db   = 'crud_php';

$conn = new mysqli($host, $user, $pass, $db);

if ($conn->connect_error) {
    die("Conexión fallida: " . $conn->connect_error);
}
?>
```



## Configuración para conexión remota con la BBDD (192.168.140.20)
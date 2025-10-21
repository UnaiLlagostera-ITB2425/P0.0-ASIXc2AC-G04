# Tarea 3: Configuración del servidor Web

## Configuración para conexión remota con la BBDD (192.168.140.20)

### Estructura de la aplicación PHP y archivos principales

El código fuente se ubicará en `/var/www/html/` en el servidor web.

Archivos principales de la aplicación:

```
index.html
x.php
x.php
x.php
db.php
```

***

## X. Archivo `db.php` para conexión a la base de datos MySQL

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

---
---
---
---
---
***

## 5. Archivo `index.php` para mostrar lista de usuarios y formulario para añadir

```php
<?php
ini_set('display_errors', 1);
ini_set('display_startup_errors', 1);
error_reporting(E_ALL);

include 'db.php';
?>
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>CRUD mínimo</title>
</head>
<body>
    <h1>Lista de usuarios</h1>
    <table border="1">
        <tr><th>ID</th><th>Nombre</th><th>Email</th><th>Acciones</th></tr>
        <?php
        $result = $conn->query("SELECT * FROM users");
        while ($row = $result->fetch_assoc()) {
            echo "<tr>
                    <td>{$row['id']}</td>
                    <td>{$row['name']}</td>
                    <td>{$row['email']}</td>
                    <td>
                        <a href='./edit.php?id={$row['id']}'>Editar</a> |
                        <a href='./delete.php?id={$row['id']}'>Eliminar</a>
                    </td>
                 </tr>";
        }
        ?>
    </table>

    <h2>Agregar usuario</h2>
    <form action="./add.php" method="post">
        Nombre: <input type="text" name="name" required>
        Email: <input type="email" name="email" required>
        <button type="submit">Agregar</button>
    </form>
</body>
</html>
```


***

## 6. Archivo `add.php` para insertar usuarios nuevos

```php
<?php
ini_set('display_errors', 1);
error_reporting(E_ALL);
include 'db.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $name  = $_POST['name'];
    $email = $_POST['email'];

    $stmt = $conn->prepare("INSERT INTO users (name, email) VALUES (?, ?)");
    $stmt->bind_param("ss", $name, $email);
    $stmt->execute();

    header("Location: index.php");
    exit;
}
?>
```


***

## 7. Archivo `edit.php` para editar usuarios existentes

```php
<?php
ini_set('display_errors', 1);
error_reporting(E_ALL);
include 'db.php';

if (isset($_GET['id'])) {
    $id = (int)$_GET['id'];
    $result = $conn->query("SELECT * FROM users WHERE id=$id");
    $user = $result->fetch_assoc();
}

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $id    = (int)$_POST['id'];
    $name  = $_POST['name'];
    $email = $_POST['email'];

    $stmt = $conn->prepare("UPDATE users SET name=?, email=? WHERE id=?");
    $stmt->bind_param("ssi", $name, $email, $id);
    $stmt->execute();

    header("Location: index.php");
    exit;
}
?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Editar usuario</title>
</head>
<body>
    <h1>Editar usuario</h1>
    <form method="post">
        <input type="hidden" name="id" value="<?= $user['id'] ?>">
        Nombre: <input type="text" name="name" value="<?= $user['name'] ?>" required>
        Email: <input type="email" name="email" value="<?= $user['email'] ?>" required>
        <button type="submit">Guardar</button>
    </form>
</body>
</html>
```


***

## 8. Archivo `delete.php` para borrar usuarios

```php
<?php
ini_set('display_errors', 1);
error_reporting(E_ALL);
include 'db.php';

$id = (int)$_GET['id'];
$conn->query("DELETE FROM users WHERE id=$id");

header("Location: index.php");
exit;
?>
```


***

## 9. Archivo `testdb.php` para probar la conexión a la base de datos

```php
<?php
ini_set('display_errors', 1);
error_reporting(E_ALL);

$host = '192.168.140.10';
$user = 'bchecker';
$pass = 'bchecker121';
$db   = 'crud_php';

$conn = new mysqli($host, $user, $pass, $db);

if ($conn->connect_error) {
    die("Error de conexión: " . $conn->connect_error);
}

echo "Conexión OK!";
?>
```

Acceder en navegador:
`http://192.168.40.10/testdb.php`

***

## 10. Enlace simbólico para vincular carpeta del proyecto al servidor web

Si el proyecto está en `/home/isard/Projectes_1/app` y quieres que aparezca en `/var/www/html` ejecuta:

```bash
sudo rm -rf /var/www/html
sudo ln -s /home/isard/Projectes_1/app /var/www/html
```

Esto crea un enlace simbólico que hará que los cambios en el proyecto se reflejen inmediatamente sin copiar manualmente.

***

## 11. Permisos recomendados para la carpeta del proyecto

```bash
sudo chown -R www-data:www-data /home/isard/Projectes_1/app
sudo chmod -R 755 /home/isard/Projectes_1/app
```


***

## 12. Verificación final

1. Abre en navegador:
`http://192.168.40.10/index.php`
2. Comprueba que:

- Se muestra correctamente la tabla de usuarios (vacía o con datos).
- El formulario para agregar usuarios funciona.
- Puedes editar y eliminar usuarios sin problemas.

***

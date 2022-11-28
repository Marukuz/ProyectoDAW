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
![imagen](https://user-images.githubusercontent.com/91668406/204241528-3d6e3ed6-0467-4864-8b50-4eddcd5a7eca.png)

Al igual que las credenciales de nuestra base de datos creada anteriormente.

![imagen](https://user-images.githubusercontent.com/91668406/204241750-e7d3d294-9111-44c8-a8ee-93db3ad28c8d.png)

Seguidamente asignamos en /etc/apache2/sites-available/000-default.conf el dominio “centro.intranet” al directorio donde esta instalado el wordpress, para, que al entrar por este dominio nos rediriga a wordpress automaticamente.

```bash
  sudo nano /etc/apache2/sites-available/000-default.conf
  
  <VirtualHost*:80>
    ServerName centro.intranet
    ServerAlias www.centro.intranet
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/wordpress
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
```

![imagen](https://user-images.githubusercontent.com/91668406/204242489-4db77f91-5171-4d3e-9b82-87df86db7bf8.png)

Ahora si entramos desde el dominio centro.intranet nos saldrá la pantalla de instalación de wordpress.

![imagen](https://user-images.githubusercontent.com/91668406/204244214-9418f233-431f-4a7b-829b-652b81ec921d.png)


### ·Activar el módulo “wsgi” para permitir la ejecución de aplicaciones Python

Descargamos el modulo WSGI y lo activamos con el siguiente comando:

```bash
  sudo apt install libapache2-mod-wsgi-py3
  sudo a2enmod wsgi
```
![imagen](https://user-images.githubusercontent.com/91668406/204244617-bf68712b-927e-490a-bd15-ef0fdb920e40.png)

### ·Crea y despliega una pequeña aplicación python para comprobar que funciona correctamente.

Creamos un script .py con el siguiente comando y introducimos lo siguiente:

```bash
  sudo nano /var/www/html/app.py
```
```python3
def application(environ, start_response):
    status = '200 OK'
    output = b'APP CON PYTHON EJECUTADA DESDE WSGI EN APACHE\n'
    response_headers = [('Content-type', 'text/plain'),
                        ('Content-Length', str(len(output)))]
    start_response(status, response_headers)
    return [output] 
```

![imagen](https://user-images.githubusercontent.com/91668406/204246050-815ab779-7925-46ab-8940-793921b951ce.png)

Cambiamos los permisos con el siguiente comando:

```bash
  sudo chown www-data:www-data /var/www/html/app.py
  sudo chmod 775 /var/www/html/app.py
```
![imagen](https://user-images.githubusercontent.com/91668406/204246606-b1b0d16f-0832-4a30-92c2-0228bef9fa3c.png)

Finalmente añadimos en el archivo /etc/apache2/sites-available/000-default.conf lo siguiente para poder acceder al script mediante “http://departamentos.centro.intranet/app”. 

```bash
  sudo nano /etc/apache2/sites-available/000-default.conf
 
  WSGIScriptAlias /app /var/www/html/app.py
```

![imagen](https://user-images.githubusercontent.com/91668406/204247320-bf83e3fa-89ff-4b64-a5ad-3af06402a3a2.png)

Y así se vería una vez entramos desde la web:

![imagen](https://user-images.githubusercontent.com/91668406/204247491-49fbbf5f-03ab-4225-8372-fbe799f8f852.png)


### ·Adicionalmente protegeremos el acceso a la aplicación python mediante autenticación

### ·Instala y configura awstat.

### ·Instala un segundo servidor de tu elección (nginx, lighttpd) bajo el dominio “servidor2.centro.intranet”. 
###  Debes configurarlo para que sirva en el puerto 8080 y haz los cambios necesarios para ejecutar php. Instala phpmyadmin.

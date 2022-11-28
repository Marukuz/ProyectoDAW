# Práctica Servidores web 1º trimestre

### ·Instalación del servidor web apache. Usaremos dos dominios mediante el archivo hosts: centro.intranet y departamentos.centro.intranet. El primero servirá el contenido mediante wordpress y el segundo una aplicación en python.

Hacemos un update de los repositorios antes de descargar apache

```bash
  sudo apt update 
```
![image](https://user-images.githubusercontent.com/91668406/204348406-73154743-550d-4cdc-a8eb-f0021540c321.png)

Instalamos el paquete de Apache

```bash
  sudo apt install apache2
```
![image](https://user-images.githubusercontent.com/91668406/204348470-8ed6cf6b-0d26-4261-b15a-f762328bfaf5.png)

Comprobamos que se ha instalado correctamente


```bash
  sudo service apache2 status
```

![image](https://user-images.githubusercontent.com/91668406/204348509-60de23fc-6f8b-4815-a519-9c20ceef27da.png)

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

Creamos el archivo con "-c" y seguidamente un usuario con contraseña con el siguiente comando:

```bash
  htpasswd -c /etc/apache2/.htpasswd marc
```

![image](https://user-images.githubusercontent.com/91668406/204350024-e68708f1-ee29-4fac-9d73-8198a1748f06.png)

Ahora nos dirigimos al archivo de configuracion de apache2 con el siguiente comando:

```bash
  sudo nano /etc/apache2/sites-available/000-default.conf
```
Y introducimos lo siguiente: 

```bash
<Directory /var/www/html/app.py>
        AuthType Basic
        AuthName "Autenticacion Obligatorio"
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
</Directory>
```
Como podemos observar hemos restringido nuestra aplicacion python "app.py" para los usuarios que esten dentro del archivo que hemos creado anteriormente con nuestro usuario.

![image](https://user-images.githubusercontent.com/91668406/204351872-a3aa1b0d-10bf-4928-b90f-a008819f5955.png)

Aqui un ejemplo de como se muestra cuando intentamos entrar a la app via web:

![image](https://user-images.githubusercontent.com/91668406/204352063-109afda5-8495-4e17-991a-36019423653b.png)

### ·Instala y configura awstat.

Instalamos awstat con el siguiente comando:

```bash
  sudo apt install awstats
```

![image](https://user-images.githubusercontent.com/91668406/204352749-4e43b559-d1db-48af-838c-12645b7c5143.png)

Seguidamente habilitamos el modulo CGI y reiniciamos apache2:

```bash
  a2enmod cgi
  systemctl restart apache2
```

![image](https://user-images.githubusercontent.com/91668406/204352893-121503bd-5815-4a2e-85ab-6fa8fa56bf32.png)

Creamos un archivo de configuracion para el dominio "centro.intranet" en nuestro caso con el siguiente comando:

```bash
  sudo cp /etc/awstats/awstats.conf /etc/awstats/awstats.centro.intranet.conf
```

![image](https://user-images.githubusercontent.com/91668406/204356835-157e753f-5fcb-4991-b9fe-eda4051ffaef.png)

Ahora entramos al archivo de configuracion con el siguiente comando:

```bash
  sudo nano /etc/awstats/awstats.centro.intranet.conf
``` 

Y cambiamos los siguiente:

```bash
  SiteDomain=»centro.intranet»
  HostAliases=»centro.intranet localhost 127.0.0.1″
```

![image](https://user-images.githubusercontent.com/91668406/204356970-9044e577-23cc-4e43-947b-cd1181ece64b.png)

Finalmente accedemos desde el siguiente link en un navegador "http://centro.intranet/cgi-bin/awstats.pl?config=centro.intranet" y nos mostrara esta web donde estara ejecutado el AWSTATS

![image](https://user-images.githubusercontent.com/91668406/204357303-0a57e50c-73e0-4fdf-917c-bd73d95a4a07.png)

### ·Instala un segundo servidor de tu elección (nginx, lighttpd) bajo el dominio “servidor2.centro.intranet”. Debes configurarlo para que sirva en el puerto 8080 y haz los cambios necesarios para ejecutar php. Instala phpmyadmin.

Instalamos NGINX con el siguiente comando:
```bash
  apt install nginx mlocate
```

![image](https://user-images.githubusercontent.com/91668406/204364860-feaee5c3-7074-428e-b2ae-07830353bc9a.png)

Instalamos el paquete PHP:

```bash
  apt install php-fpm
```

![image](https://user-images.githubusercontent.com/91668406/204365005-6f5dfb18-25f3-42da-8b83-41195efac469.png)

Instanciamos el nuevo servidor (dominio) en /etc/hosts:

![image](https://user-images.githubusercontent.com/91668406/204382981-ec3b2335-9c01-4c1f-9a5e-576606c45341.png)

Instalamos y descomprimimos el phpmyadmin:

```bash
  sudo wget https://files.phpmyadmin.net/phpMyAdmin/5.2.0/phpMyAdmin-5.2.0-all-languages.tar.gz
  tar -zvf phpMyAdmin-5.2.0-all-languages.tar.gz
```
  
Movemos el php a nuestra carpeta donde tendremos phpmyadmin /var/www/phpmyadmin

```bash
  sudo mv phpMyAdmin-4.9.5-all-languages /var/www/phpmyadmin
```

Seguidamente movemos la plantilla del archivo de configuracion de PHPMYADMIN y le cambiamos el nombre.

```bash
  sudo cp /var/www/phpmyadmin/config.sample.inc.php /var/www/phpmyadmin/config.inc.php
```

Generamos el "blowfish_secret" con este comando

```bash
  sudo apt install pwgen
  pwgen -s 32 1
```

Entramos al archivo de configuracion con el siguiente comando:

```bash
  sudo nano /var/www/phpmyadmin/config.inc.php
```
Y hacemos los siguientes cambios:

```bash
  $cfg['blowfish_secret'] = 'codigo generado automaticamente anteriormente';
  $cfg['Servers'][$i]['controlhost'] = 'localhost';
  $cfg['Servers'][$i]['controlport'] = '';
  $cfg['Servers'][$i]['controluser'] = 'marc';
  $cfg['Servers'][$i]['controlpass'] = 'password';
```

![image](https://user-images.githubusercontent.com/91668406/204385182-72f8fe1c-e0d5-4c3f-8fa6-b2145f70e6ce.png)
![image](https://user-images.githubusercontent.com/91668406/204385237-7dc279ff-50aa-44b3-8321-65a60f3c78fb.png)

Importamos la base de datos que ya viene incluida en phpmyadmin por defecto:

```bash
  sudo mysql < /var/www/phpmyadmin/sql/create_tables.sql -u root -p
```

![image](https://user-images.githubusercontent.com/91668406/204385349-bc990cd2-b684-4ead-bdad-1e8295ff371f.png)

Nos damos permisos a nuestro usuario a la base de datos de phpmyadmin:

![image](https://user-images.githubusercontent.com/91668406/204385619-2e07f0d0-8d04-46b2-b950-bb1dc6134036.png)

Configuramos el servidor para poder acceder a phpmyadmin con el siguiente comando:

```bash
  sudo nano /etc/nginx/sites-available/phpmyadmin.conf
```

![image](https://user-images.githubusercontent.com/91668406/204385854-36a3ef13-133d-4e0f-8f9e-f13485515c94.png)

Y añadimos lo siguiente:

![image](https://user-images.githubusercontent.com/91668406/204386019-f02a2eb6-a4e3-48d0-9ff4-ca59c702528b.png)

Para finalizar cambiamos los permisos de phpmyadmin con el siguiente comando y reiniciamos los servicios.

```bash
  sudo chown -R www-data:www-data /var/www/phpmyadmin
  sudo systemctl restart nginx php8.1-fpm  
```

Si accedemos con "servidor2.centro.intranet:8080 nos saldra la siguiente ventana:

![image](https://user-images.githubusercontent.com/91668406/204386376-5e1a3f4e-1ca8-4a5d-b7c9-21516b843f7c.png)

Y si ponemos las credenciales entraremos dentro del phpmyadmin:

![image](https://user-images.githubusercontent.com/91668406/204379493-76d4ee13-37a7-409a-b635-23417832d49d.png)



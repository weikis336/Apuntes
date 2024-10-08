Instalación de Wordpress en Virtualbox con Ubuntu Server

# 1. Instalamos las dependencias


```bash
sudo apt update

sudo apt install mysql-server apache2 php-fpm libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip
```

# 2. Configuramos el comportamiento de PHP 

```bash
cd /etc/php/XX/apache2
sudo nano php.ini
```

# Dentro buscamos y modificamos los siguientes valores
```
post_max_size = 150M  (un poco mayor para aceptar las cabeceras)
upload_max_filesize = 100M
max_execution_time = 120
memory_limit = 128M
```

# 3. Configuramos el comportamiento de la base de datos
```bash
sudo mysql
```
```SQL
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'mynewpassword';

EXIT
```

```bash
sudo mysql_secure_installation (contestar a la primera pregunta no y al resto yes).
```

# 4. Creamos la base de datos “wordpress”, creamos el usuario “wordpressuser” con la contraseña “password” y le damos privilegios. 
```bash
sudo mysql
```
```SQL
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;

CREATE USER 'wordpressuser'@'%' IDENTIFIED BY 'password';

GRANT ALL ON wordpress.* TO 'wordpressuser'@'%';
 
FLUSH PRIVILEGES;

EXIT;
```

# 5. Configuramos el servidor web para que apunte a la carpeta wordpress
```bash
cd /etc/apache2/sites-available

sudo nano 000-default.conf
```

cambiamos 
```
/var/www/html > /var/www/html/wordpress
```


guardamos y salimos
```bash
sudo a2enmod rewrite

sudo systemctl restart apache2
```

# 6. Descargamos wordpress, descomprimimos el archivo tar.gz descargado y le damos permisos
                                
```bash
cd /tmp
sudo curl -O https://wordpress.org/latest.tar.gz
sudo tar xf latest.tar.gz -C /var/www/html/
sudo chown -R www-data:www-data /var/www/html/wordpress/
```

# 7. Configuramos finalmente Wordpress con el nombre de la base de datos, el usuario de la base de datos y su contraseña. 

```bash
sudo cp /var/www/html/wordpress/wp-config-sample.php /var/www/html/wordpress/wp-config.php

sudo nano /var/www/html/wordpress/wp-config.php
```

```
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wordpressuser' );
define( 'DB_PASSWORD', 'password' );
define( 'DB_HOST', 'localhost' );
define( 'DB_CHARSET', 'utf8' );
```

# 8. Miramos que ip tenemos con ifconfig y accedemos a Wordpress a través de ella en el navegador.

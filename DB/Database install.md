## Instalacion de paquetes de mariadb

```shell
sudo apt install mariadb-server -y
```

## Configuracion basica de mysql


``` bash
sudo mysql_secure_installation

sudo systemctl restart mysql
```


## Creacion de una base de datos y usuario SQL

```shell
sudo mysql
```


``` sql
CREATE DATABASE <nombre_base_datos> DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
CREATE USER 'nombre_usuario'@'%' IDENTIFIED BY 'password';
GRANT ALL ON nombre_base_datos.* TO 'nombre_usuario'@'%';
FLUSH PRIVILEGES;
EXIT;
```

## Dar privilegios en todas las bases de datos del server
```SQL
  GRANT ALL PRIVILEGES ON *.* TO 'umbreon'@'0.0.0.0';
```


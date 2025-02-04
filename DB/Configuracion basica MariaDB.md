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

## Dar privilegios en todas las bases de datos del server en todas las direcciones
```SQL
  GRANT ALL PRIVILEGES ON *.* TO 'usuario'@'0.0.0.0';
```
## Dar privilegios en todas las bases de datos del server en un segmento de red
```SQL
  GRANT ALL PRIVILEGES ON *.* TO 'usuario'@'192.168.0.%';
```


## Permitir accesos desde fuera del localhost

Edita la configuracion del mysql para permitir accesos desde fuera del mysql.
El fichero a modificar es el /etc/mysql/mariadb.cnf

```SQL
[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
log-error       = /var/log/mysql/error.log
bind-address    = 0.0.0.0
port            = 3306

server-id = 2             # Unique server ID (use 2 for the slave)
log_bin = /var/lib/mysql/mysql-bin
```


## Mysql Server Main

```SQL
CREATE USER 'replicator'@'%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.* TO 'replicator'@'%';
FLUSH PRIVILEGES;
SHOW MASTER STATUS; ## para saber si el master está OK
```
Edit config file /etc/mysql/my.cnf y agregar estas lineas

```
server-id = 1             # Unique server ID (use 1 for the master)
log_bin = /var/lib/mysql/mysql-bin
```

## Mysql Server Semilla

```SQL
CHANGE MASTER TO
    MASTER_HOST='192.168.1.10',
    MASTER_USER='replicator',
    MASTER_LOG_FILE='mysql-bin.<NombreDelUltimoLog>'; ## este fichero debería existir en el master en /var/lib/mysql/mysql-bin
    MASTER_PASSWORD='password';
START SLAVE;

SHOW SLAVE STATUS\G;
```
Edit config file /etc/mysql/my.cnf y agregar estas lineas

```
[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
log-error       = /var/log/mysql/error.log
bind-address    = 0.0.0.0
port            = 3306

server-id = 2             # Unique server ID (use 2 for the slave)
log_bin = /var/lib/mysql/mysql-bin
server-id = 2             # Unique server ID (use 2 for the slave)
log_bin = /var/lib/mysql/mysql-bin
```

## Mysql Server Main

Si esta bien configurado deberia de replicarse los cambios que hagamos en las bases de datos semillas. 

```SQL
CREATE DATABASE test_db;
USE test_db;
CREATE TABLE test_table (id INT PRIMARY KEY, name VARCHAR(50));
INSERT INTO test_table (id, name) VALUES (1, 'Replication Test');
```

## Mysql Server Main

```SQL
CREATE USER 'replicator'@'%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.* TO 'replicator'@'%';
FLUSH PRIVILEGES;
SHOW MASTER STATUS; ## para saber si el master está OK
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

## Mysql Server Main

Si esta bien configurado deberia de replicarse los cambios que hagamos en las bases de datos semillas. 

```SQL
CREATE DATABASE test_db;
USE test_db;
CREATE TABLE test_table (id INT PRIMARY KEY, name VARCHAR(50));
INSERT INTO test_table (id, name) VALUES (1, 'Replication Test');
```
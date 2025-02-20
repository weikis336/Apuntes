## Static IP
run as root
```shell
nano /etc/network/interfaces

# The loopback network interface  
auto lo  
iface lo inet loopback  
  
# The primary network interface  
allow-hotplug enp0s3  
auto enp0s3  
iface enp0s3 inet static  
address 192.168.1.X/24  
gateway 192.168.1.1  
nameserver 192.168.1.250
```
## Save the document and then change the network on the vbox config to bridge
run as root
```shell
/etc/init.d/networking restart
```

## Update the distro
run as root
```shell
apt update && apt upgrade && apt install sudo
```
Then reboot
```shell
reboot
```

## Make your sudo user
run as root 
```shell
 useradd -m -s /bin/bash -G sudo <username> &&  passwd <username>
```
If the user already exists by default is joan

```shell
usermod -aG sudo joan
```

## Mysql to slave

On the mysql server make an user to replicate the masterDB
On this guide the user will be "replicator"


### MasterSQL
```mysql
CREATE USER 'pilot'@'%' IDENTIFIED BY 'gelpens';
GRANT SELECT ON *.* TO 'pilot'@'%';
FLUSH PRIVILEGES;
SHOW MASTER STATUS;
```
```shell
sudo nano /etc/mysql/my.cnf
```
Add on the end of the file
```
server-id = 1             # Unique server ID (use 1 for the master)
log_bin = /var/lib/mysql/mysql-bin
```
Restart the mysql service
```shell
sudo systemctl restart mysql
```

Crate of the replication user
```shell
sudo mysql -U
```
Then on the mysql shell

```mysql
CREATE USER 'replicator'@'%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.* TO 'replicator'@'%';
FLUSH PRIVILEGES;

SHOW MASTER STATUS;
```
Keep in mind the file of show master status and her position.
### Slave SQL
On the slave follow these steps.
```shell
sudo nano /etc/mysql/my.cnf
```
Add on the end of the file
```
server-id = 2             # Unique server ID (use 1 for the master)
log_bin = /var/lib/mysql/mysql-bin
```
Restart the mysql service
```shell
sudo systemctl restart mysql
```
Log on the mysql
```shell
sudo mysql -U root
```
Change to your settings. Master host, master log file and master log pos

Check the  Master Log File
```sql
CHANGE MASTER TO
    MASTER_HOST='192.168.1.X',
    MASTER_USER='replicator',
    MASTER_LOG_FILE='mysql-bin.000008', ## este fichero debería existir en el master en /var/lib/mysql/mysql-bin
    MASTER_LOG_POS=615,
    MASTER_PASSWORD='password';
START SLAVE;
```
Check if the settings are well applied

```sql
SHOW SLAVE STATUS\G;
```

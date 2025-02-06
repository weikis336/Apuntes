## Installing Samba
We need to use one of the properties of the samba service. Its easy to install the samba service on debian.
```shell
sudo apt install samba
```

## Configure the netbios on Samba

When the samba service its installed. Edit this file on this path.
/etc/samba/smb.conf

```shell
sudo nano /etc/samba/smb.conf
```

On the end of the file. Add and modify to your server.

```shell
[global]
workgroup = WORKGROUP
server string = Samba Server %v
netbios name = linuxserver # Where its suposed to be your server hostname
security = user
map to guest = bad user
dns proxy = no

```
Save the file. 
Reload the samba service with.
```shell 
sudo systemctl start smbd
sudo systemctl enable smbd
sudo ufw allow Samba
```

Then modify the /etc/hosts

```shell
sudo nano /etc/hosts
```

On the file you may find something like this.

```shell
/etc/hosts     
127.0.0.1       localhost
127.0.1.1       #where your hostname needs to be

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

Save the file.
Then reboot the server to ensure the changes.
```shell
sudo reboot
```

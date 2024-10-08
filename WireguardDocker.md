
## Docker compose de wireguard
```
version: "3.8"
  services:
    wireguard:
      image: lscr.io/linuxserver/wireguard:latest
      container_name: wireguard
      cap_add:
        - NET_ADMIN
        - SYS_MODULE # optional, only needed if you require module loading
      environment:
        - PUID=1001  # ID de usuario de docker
        - PGID=100  # ID de grupo del usuario docker
        - TZ=Europe/Madrid 
        - SERVERURL=Direccion publica del server # Change to your domain or public IP
        - SERVERPORT=51820 # Puerto de Wireguard
        - PEERS=5 # Numero de usuarios a generar
        - PEERDNS=auto # DNS de la VPN
        - INTERNAL_SUBNET=172.16.8.0 # Subred de la vpn
        - ALLOWEDIPS=0.0.0.0/0 # Direcciones desde donde permitiremos la conexion a la VPN 0.0.0.0 todo Internet
        - PERSISTENTKEEPALIVE_PEERS= # Tiempo de persistencia de la conexion
        - LOG_CONFS=true # Logs habilitados
      volumes:
        - /Docker/Wireguard/wireguard:/config # Lugar donde se van a guardar los datos del docker
        - /lib/modules:/lib/modules # Lugar donde se guardan las librerias de wireguard
      ports:
        - 51820:51820/udp # Puertos
      sysctls:
        - net.ipv4.conf.all.src_valid_mark=1
      restart: unless-stopped
```

## Extraer los confs para configurar wireguard

```
scp -r Usuario@IPSERVER:/Docker/Wireguard/wireguard/peer1/* C:\Users\Usuario\Documents\
```


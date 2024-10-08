## Docker Compose basico de MariaDB 

```
version: '3.8'
services:
  mariadb:
    image: mariadb:latest  # Use the official MariaDB image
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ContraseñaRoot  # Set a root password
      MYSQL_PASSWORD: ContraseñaUsuario        # Set a password for the user
      MYSQL_USER: Usuario        # User to create
      MYSQL_DATABASE: BBD a crear       # Optional: create a database
    ports:
      - "3306:3306"
    volumes:
      - /Docker/MariaDB/maria-db:/var/lib/mysql ###Ruta donde MariaDB guardara sus datos
```

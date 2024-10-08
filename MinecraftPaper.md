## Servidor de Minecraft 1.21

```
version: "3.6"
services:
  mcserver:
    container_name: "mcserver"
    entrypoint:
      - "/opt/minecraft/docker-entrypoint.sh"
    environment:
      - "MEMORYSIZE=1G"
      - "PATH=/opt/java/openjdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
      - "JAVA_HOME=/opt/java/openjdk"
      - "LANG=en_US.UTF-8"
      - "LANGUAGE=en_US:en"
      - "LC_ALL=en_US.UTF-8"
      - "JAVA_VERSION=jdk-21.0.4+7"
      - "JAVAFLAGS=-Dlog4j2.formatMsgNoLookups=true -XX:+UseG1GC -XX:+ParallelRefProcEnabled\
        \ -XX:MaxGCPauseMillis=200 -XX:+UnlockExperimentalVMOptions -XX:+DisableExplicitGC\
        \ -XX:+AlwaysPreTouch -XX:G1NewSizePercent=30 -XX:G1MaxNewSizePercent=40 -XX:G1HeapRegionSize=8M\
        \ -XX:G1ReservePercent=20 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=4\
        \ -XX:InitiatingHeapOccupancyPercent=15 -XX:G1MixedGCLiveThresholdPercent=90\
        \ -XX:G1RSetUpdatingPauseTimePercent=5 -XX:SurvivorRatio=32 -XX:+PerfDisableSharedMem\
        \ -XX:MaxTenuringThreshold=1 -Dusing.aikars.flags=mcflags.emc.gs -Dcom.mojang.eula.agree=true"
      - "PAPERMC_FLAGS=--nojline"
    hostname: "196da1ff7685"
    image: "marctv/minecraft-papermc-server:latest"
    ipc: "private"
    labels:
      org.mojang.minecraft.papermc.binary: "https://papermc.io/api/v2/projects/paper/versions/1.21.1/builds/105/downloads/paper-1.21.1-105.jar"
      org.mojang.minecraft.papermc.build: "105"
      org.mojang.minecraft.papermc.full_version: "1.21.1-105"
      org.mojang.minecraft.papermc.type: "stable"
      org.mojang.minecraft.papermc.url: "https://api.papermc.io/v2/projects/paper/versions/1.21.1/builds/105"
      org.mojang.minecraft.papermc.version: "1.21.1"
      org.opencontainers.image.authors: "Marc Tönsing <marc@marc.tv>"
      org.opencontainers.image.created: "2024-09-28T01:12:59.997Z"
      org.opencontainers.image.description: "Starts a Minecraft PaperMC server"
      org.opencontainers.image.licenses: "MIT"
      org.opencontainers.image.ref.name: "ubuntu"
      org.opencontainers.image.revision: "b6d0f047d13a30be7c3e663281ebe30dacd1af61"
      org.opencontainers.image.source: "https://github.com/mtoensing/Docker-Minecraft-PaperMC-Server"
      org.opencontainers.image.title: "Docker-Minecraft-PaperMC-Server"
      org.opencontainers.image.url: "https://github.com/mtoensing/Docker-Minecraft-PaperMC-Server"
      org.opencontainers.image.version: "1.21.1"
    logging:
      driver: "json-file"
      options: {}
    mac_address: "02:42:ac:11:00:02"
    network_mode: "bridge"
    ports:
      - "25565:25565/tcp"
    stdin_open: true
    volumes:
      - "/Docker/Minecraft/mcserver:/data"
    working_dir: "/data"
```

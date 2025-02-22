#NVIDIA
## Instalar el driver en Debian12 Bookworm
#Debian #Cuerdos 

Agregas el repo contrib de debian, editando el fichero /etc/apt/sources.list

```shell
sudo nano /etc/apt/sources.list

 deb http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
```

Actualizas la lista de paquetes, e instalas.

```shell 
sudo apt install nvidia-driver firmware-misc-nonfree
```

Reinicias y comprueba que te puedas comunicar con el modulo del kernel de Nvidia.
## Comprueba que el modulo de nvidia se carga correctamente
```shell
sudo cat /sys/module/nvidia_drm/parameters/modeset
```
Si despues del comando te devuelve N añades esto. Tendras que rehacer los parametros de arranque. Apartado siguiente te enseño como. 

```shell
sudo su
	 echo "options nvidia-drm modeset=1" >> /etc/modprobe.d/nvidia-options.conf
```

Si la Gpu sigue sin activar ningún modulo procede a pasar el modeset por grub. Generalmente se aplica al instalar el driver pero puede suceder que por lo que sea no se haga.

```shell
sudo su 
echo 'GRUB_CMDLINE_LINUX="$GRUB_CMDLINE_LINUX nvidia-drm.modeset=1"' > /etc/default/grub.d/nvidia-modeset.cfg

sudo update-grub
```
## Rehacer initramfs despues de cambios en el modulo de nvidia
```shell
sudo update-initramfs -u
```
## Solventar modo Suspensión
En debian tenemos que instalar un paquete mas y proceder a habilitar sus servicios por si tenemos problemas con el modo suspensión.
```shell
sudo apt install nvidia-suspend-common

sudo systemctl enable nvidia-suspend.service
sudo systemctl enable nvidia-hibernate.service
sudo systemctl enable nvidia-resume.service
```
## Muestra los parametros soportados por la tarjeta grafica 

El comando siguiente muestra por pantalla los estados de la configuración de la tarjeta gráfica.

Primero necesitamos saber el Pci ID.
Con 
```shell
nvidia-smi 
```

```shell
sudo cat /proc/driver/nvidia/gpus/0000\:01\:00.0/power
```
## Muestra Soporte a estados D de todos los PCI-e
```shell
sudo journalctl -xb -1 | grep -e PME#
```

D0 estado en ejecución
D3Hot Bajo consumo en activo
D3Cold Muy bajo consumo con el core "Dormido"
## Pasa soporte a estados de energia automatico
Para que el driver de nvidia pueda apagar y encender el core tendremos que sobrescribir alterando el bus el parametro de power/control. 
``` shell
echo auto > /sys/bus/pci/devices/0000\:01\:00.0/power/control
```
## Parametros útiles Modulo Nvidia
Estas son las reglas del driver de nvidia que emnpleo
Opciones a modificar / añadir en el fichero /etc/modprobe.d/nvidia-options.conf
```
options nvidia-drm modeset=1
options nvidia-current fbdev=1
options nvidia-current NVreg_PreserveVideoMemoryAllocations=1 #Permite mantener en memoria la vram en la ram del equipo
options nvidia-current NVreg_EnableGpuFirmware=0 #Desactiva GSP
options nvidia-current "NVreg_DynamicPowerManagement=0x02" #Modos de energia afecta al D3
options nvidia-current NVreg_EnableS0ixPowerManagement=1 #Gestion de energia de las memorias
options nvidia-current NVreg_DynamicPowerManagementVideoMemoryThreshold 200  #El treshold de vram a partir del cual apagar o no la vram
```
DynamicPowerManagement elige el modo de perfil de energia. 
1 A todo meter nunca se apaga y sera mas agresivo.
2 Fine grade en Turing y Ampere. Permite a la tarjeta entrar en modo reposo apagandose por llamadas ACPI si el CPU, placa base y GPU lo soportan. D3Cold
3 Default en Turing y Ampere. En Ada Love Lace es Fine grade

### Con el sigiente comando comprobamos los parametros cargados
```
sudo cat /proc/driver/nvidia/params 
```
## Rehacer reglas Udev

Reglas udev fundamentales para apagar  https://wiki.archlinux.org/title/Hybrid_graphics

Reglas que nos interesan las del capitulo 22 de la documentación del driver de NVIDIA y unas mas que nos da la Arch Wiki. https://wiki.archlinux.org/title/PRIME

Escribe un archivo con este nombre  y en esta ruta/etc/udev/rules.d/80-nvidia-pm.rules
```/etc/udev/rules.d/80-nvidia-pm.rules

# Enable runtime PM for NVIDIA VGA/3D controller devices on driver bind
ACTION=="bind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030000", TEST=="power/control", ATTR{power/control}="auto"
ACTION=="bind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030200", TEST=="power/control", ATTR{power/control}="auto"

# Disable runtime PM for NVIDIA VGA/3D controller devices on driver unbind
ACTION=="unbind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030000", TEST=="power/control", ATTR{power/control}="on"
ACTION=="unbind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030200", TEST=="power/control", ATTR{power/control}="on"

# Enable runtime PM for NVIDIA VGA/3D controller devices on adding device
ACTION=="add", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030000", TEST=="power/control", ATTR{power/control}="auto"
ACTION=="add", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030200", TEST=="power/control", ATTR{power/control}="auto"
```
## Reactivar Wayland en portatiles nvidia

Muy posiblemente si usas Gnome, gdm banée las configuraciones hibridas. Demos las gracias a Debian por tener paquetes tan antiguos por estos problemas ya resueltos en versiones del escritorio modernas.  No es el fin del mundo. El driver desde la versión 470 funciona relativamente bien con wayland.
Puedes desactivar esta comprobación de Gnome haciendo lo siguiente. 

Editar el ficher /usr/lib/udev/rules.d/61-gdm.rules

En la función gdm_hybrid_laptop_check Comentar GOTO= "gdm_disable_wayland"

```
# If this is a hybrid graphics laptop with vendor nvidia driver, disable wayland
LABEL="gdm_hybrid_nvidia_laptop_check"
TEST!="/run/udev/gdm-machine-is-laptop", GOTO="gdm_hybrid_nvidia_laptop_check_end"
TEST!="/run/udev/gdm-machine-has-hybrid-graphics", GOTO="gdm_hybrid_nvidia_laptop_check_end"
TEST!="/run/udev/gdm-machine-has-vendor-nvidia-driver", GOTO="gdm_hybrid_nvidia_laptop_check_e>
#GOTO="gdm_disable_wayland"
LABEL="gdm_hybrid_nvidia_laptop_check_end"
```
### También necesitaremos activar en el modulo de nvidia este parámetro. Como hicimos antes agregando el parámetro si no lo esta. 
```shell
options nvidia NVreg_PreserveVideoMemoryAllocations=1
```

## Cuda

```shell
sudo apt install nvidia-cuda-dev nvidia-cuda-toolkit
```
## Default Nvidia Debian 
El driver propietaroo de debian a diferencia del stable de NVIDIA funciona algo diferente y emplea ciertos alias. Por si eliminas este fichero dejo la configuración default. 
Configuración del fichero /etc/modprobe.d/nvidia.conf
```
install nvidia modprobe -i nvidia-current $CMDLINE_OPTS

install nvidia-modeset modprobe nvidia ; modprobe -i nvidia-current-modeset $CMDLINE_OPTS

install nvidia-drm modprobe nvidia-modeset ; modprobe -i nvidia-current-drm $CMDLINE_OPTS

install nvidia-uvm modprobe nvidia ; modprobe -i nvidia-current-uvm $CMDLINE_OPTS

install nvidia-peermem modprobe nvidia ; modprobe -i nvidia-current-peermem $CMDLINE_OPTS

# unloading needs the internal names (i.e. upstream's names, not our renamed files)

remove nvidia modprobe -r -i nvidia-drm nvidia-modeset nvidia-peermem nvidia-uvm nvidia

remove nvidia-modeset modprobe -r -i nvidia-drm nvidia-modeset

alias char-major-195* nvidia

# These aliases are defined in *all* nvidia modules.
# Duplicating them here sets higher precedence and ensures the selected
# module gets loaded instead of a random first match if more than one
# version is installed. See #798207.
alias   pci:v000010DEd00000E00sv*sd*bc04sc80i00*        nvidia
alias   pci:v000010DEd00000AA3sv*sd*bc0Bsc40i00*        nvidia
alias   pci:v000010DEd*sv*sd*bc03sc02i00*               nvidia
alias   pci:v000010DEd*sv*sd*bc03sc00i00*               nvidia
```

## Aplicaciones que recomiendo

Envycontrol editando a nuestros ajustes de modeset a los parametros que nos sean mas comodos.

O esta que requiere algo de trabajo para instalar pero me gusta bastante.

Supergfxctl sobretodo si empleas gnome.

Optimus-manager también esta muy bien pero esta obsoleta y no aprevecha bien la gestión hybrida de Turing+ en equipos compatibles.

Si no te importa quitar tlp. El servicio de energía de System76 ya contempla las gpus hybridas.

## Desinstalar todo los paquetes relacionados con nvidia
```bash 
sudo apt remove --purge $(dpkg --get-selections | awk '/nvidia/{print $1}')
```

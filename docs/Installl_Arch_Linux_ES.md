# Guia para Instalar Arch Linux

En esta guía estaremos describiendo de manera detallada, el proceso de instalación de la distribución GNU/Linux de liberación continua, Arch Linux. No se intenta reemplazar la guía oficial de la wiki, si no, hacer un resumen secuencial del proceso de instalación.

**NOTA:** Asegúrese de haber creado el dispositivo de almacenamiento arrancable como lo recomienda la wiki.

## Tabla de Contenido

- [Preparación](#preparación)
  - [Descargar imagen ISO y fichero Signature](#descargar-imagen-iso-y-fichero-signature)
  - [Validar imagen ISO](#validar-imagen-iso)
  - [Preparar pendrive arrancable](#preparar-pendrive-arrancable)
    - [Formatear pendrive](#formatear-pendrive)
    - [Crear pendrive arrancable](#crear-pendrive-arrancable)
- [Preinstalación](#preinstalación)
  - [Distribución del teclado](#distribución-del-teclado)
  - [Fuente de terminal](#fuente-de-terminal)
  - [Conéctese a Internet](#conéctese-a-internet)
    - [Cableada](#cableada)
    - [Inalámbrica](#inalámbrica)
      - [Red visible](#red-visible)
      - [Red oculta](#red-oculta)
  - [Modo de arranque](#modo-de-arranque)
  - [Idioma temporal](#idioma-temporal)
  - [Actualizar reloj del sistema](#actualizar-reloj-del-sistema)
- [Particionado](#particionado)
  - [Dispositivo de almacenamiento](#dispositivo-de-almacenamiento)
  - [Tablas de partición](#tablas-de-partición)
  - [Diseño de particiones y puntos de montaje](#diseño-de-particiones-y-puntos-de-montaje)
    - [Modo BIOS Legacy](#modo-bios-legacy)
    - [Modo UEFI](#modo-uefi)
    - [Tamaño de la partición de intercambio SWAP](#tamaño-de-la-partición-de-intercambio-swap)
  - [Herramientas de particionado](#herramientas-de-particionado)
  - [Formatear las particiones](#formatear-las-particiones)
  - [Montar y activar las particiones](#montar-y-activar-las-particiones)
- [Configuración temporal de pacman](#configuración-temporal-de-pacman)
  - [Forma manual](#forma-manual)
  - [Usando reflector](#usando-reflector)
  - [Usando pacman mirrorlist generator](#usando-pacman-mirrorlist-generator)
  - [Tips de mirrors](#tips-de-mirrors)
  - [Tips de pacstrap](#tips-de-pacstrap)
- [Instalación del sistema](#instalación-del-sistema)
  - [Especificación de los paquetes principales](#especificación-de-los-paquetes-principales)
  - [Modos de instalación](#modos-de-instalación)
  - [Único sistema](#único-sistema)
  - [Dual boot con Windows](#dual-boot-con-windows)
  - [Error de pacstrap](#error-de-pacstrap)
  - [Configuración del sistema](#configuración-del-sistema)
  - [Fstab](#fstab)
  - [Chroot](#chroot)
  - [Zona horaria](#zona-horaria)
  - [Localización](#localización)
  - [Configuración de red](#configuración-de-red)
  - [Initramfs](#initramfs)
  - [Contraseña root](#contraseña-root)
- [Cargador de arranque grub](#cargador-de-arranque-grub)
  - [Grub BIOS Legacy](#grub-bios-legacy)
  - [Grub UEFI](#grub-uefi)
  - [Arranque del sistema](#arranque-del-sistema)
- [Usuarios y grupos](#usuarios-y-grupos)
  - [Directorios de usuarios](#directorios-de-usuarios)
- [Configuración de pacman](#configuración-de-pacman)
  - [Colores y porcentaje de avance de descarga de paquetes](#colores-y-porcentaje-de-avance-en-descarga-de-paquetes)
  - [Animación de pacman](#animación-de-pacman)
  - [Repositorio multilib](#repositorio-multilib)
- [Firmware del procesador](#firmware-del-procesador)
- [Fuentes](#fuentes)
- [Servicios de red](#servicios-de-red)
- [Reiniciar](#reiniciar)
- [Solución de problemas](#solución-de-problemas)
- [Referencias](#referencias)

## Preparación

### Descargar imagen ISO y fichero signature

Descargue el fichero ISO y signature del espejo (`mirror`) de su país o de su preferencia, desde la página oficial, ambos en un mismo directorio.

### Validar imagen ISO

Nos ubicamos en el directorio donde descargamos los ficheros y validamos la firma `gpg` de la imagen ISO.

```shell
gpg --keyserver-options auto-key-retrieve --verify
```

Debe presentar en la salida estandar `"Good signature from..."` para confirmar la validación.

### Preparar pendrive arrancable

#### Formatear pendrive

asdadsadads'

#### Crear pendrive arrancable

afkafkasbfkjbafjksbfk

### Preinstalación

Las configuraciones que se realicen en la preinstalación serán de manera temporal, es decir, solo aplican en el modo live, así que seguramente cuando estemos en el entorno `root` del sistema instalado, volveremos a usar dichas configuraciones para hacerlas permanentes.

#### Distribución del teclado

En nuestro caso que somos de Estados Unidos, establecemos la distribución de américa latina.

```shell
loadkeys la-latin1
```

Para listar todas las posibles distribuciones use.

```shell
localectl list-keymaps
```

#### Fuente de terminal

Establecemos una fuente apropiada durante la instalación.

```shell
setfont Lat2-Terminus16
```

#### Conéctese a Internet

Se recomienda usar la conexión por cable en ves de la inalámbrica, para mayor estabilidad y velocidad del Internet.

##### Cableada

Para conexión por cable tan solo es necesario tener conectado el cable de Ethernet previo al inicio del live USB y luego hacer un ping para verificar la conexión a Internet.

```shell
ping -c3 archlinux.org
```

Si falló al hacer el ping es probable que haya conectado el cable Ethernet luego de haber iniciado el live USB, por eso lo normal es que el servicio `dhcpcd` no se haya iniciado correctamente, así que vamos a iniciar el servicio.

```shell
systemctl start dhcpcd.service
```

Ahora verifique que el cable Ethernet está bien conectado y vuelva a hacer un ping.

```shell
ping -c3 archlinux.org
```

Ya debería estar recibiendo datos al hacer ping, lo que indica que ya tiene conexión a Internet, de lo contrario reinicie y vuelva a entrar al live USB para iniciar correctamente los servicios.

##### Inalámbrica

Si desea conectarse por wifi, es conveniente primero verificar si se ha detectado y cargado correctamente el controlador de la tarjeta inalámbrica. Compruebe que en la salida del siguiente comando exista un fichero que inicie con "`wl`".

```shell
ls /sys/class/net
```

Si no existe ningún fichero que inicie con "`wl`", entonces no podrá realizar la instalación vía wifi. Si por el contrario el fichero existe, entonces será posible conectarse de forma inalámbrica usando alguna de las siguientes herramientas.

Lo primero será consultar el nombre del dispositivo inalámbrico.

```shell
ls /sys/class/net | grep "^wl"
```

En este caso nos devuelve `wlan0`.

##### Red visible

Para redes visibles usaremos la herramienta recomendada por la wiki, `IWD`, teniendo en cuenta su dispositivo inalámbrico, `ssid` y `password`.

```shell
iwctl --passphrase 'password' station wlan0 connect 'ssid'
```

##### Red oculta

Para redes ocultas usaremos la herramienta `wpa_supplicant`, teniendo en cuenta el nombre del dispositivo, `ssid` y `password`. Abrimos el archivo `/etc/wpa_supplicant/wpa_supplicant.conf` y añadimos las siguientes lineas:

```shell
network={
ssid="WIFI Home"
scan_ssid=1
key_mgmt=WPA-PSK
psk="password"
}
```

Ahora levantamos la red.

```shell
wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf
```

#### Modo de arranque

Verificamos si la placa base es compatible con el modo de arranque `UEFI`, consultando si existe el directorio especificado, de lo contrario el arranque solo es compatible con `BIOS/Legacy`.

```shell
ls /sys/firmware/efi/efivars
```

#### Idioma temporal

Vamos a configurar el idioma temporal de las herramientas disponibles a nuestro idioma español, sobre todo las de particionado. Para eso abrimos el fichero `/etc/locale.gen` y quitamos la almohadilla del idioma de nuestro país, en el caso de Estados Unidos descomentamos `es_US.UTF-8 UTF-8`. En este caso usaremos el editor VIM.

```shell
vim /etc/locale.gen
```

Bajamos hasta donde pone el idioma a descomentar, luego presionamos la tecla "`i`" para activar la edición del fichero, quitamos la almohadilla del idioma, luego presionamos la tecla "`Esc`" y presionamos "`:wq`" seguido de la tecla "`Intro`" para guardar los cambios hechos y salir del editor VIM.

Ahora vamos a generar la configuración regional.

```shell
locale-gen
```

Exportamos la variable `LANG` para finalizar la configuración regional temporal.

```shell
export LANG=es_US.UTF-8
```

#### Actualizar reloj del sistema

```shell
timedatectl set-ntp true
```

### Particionado

#### Dispositivo de almacenamiento

Vamos a identificar el dispositivo de almacenamiento donde vamos a instalar Arch Linux.

```shell
lsblk -Spo NAME,MODEL,SIZE,VENDOR,RM
```

Para diferenciar los dispositivos de almacenamiento conectados nos fijamos en las dos últimas columnas. VENDOR indica el tipo de conexión y RM si es o no extraible; uno indica que el dispositivo es extraíble y cero que no lo es. En esta guía usaremos el dispositivo `/dev/sda`.

#### Tablas de partición

Las tablas de particiones son formas de organizar y establecer características a las particiones en un dispositivo de almacenamiento. Vienen dadas por etiquetas que se les asigna al dispositivo de almacenamiento antes de crear sus particiones.

Existen muchas etiquetas pero las que nos interesan son `MBR` o `GPT`. La primera es recomendable para dispositivos de almacenamiento conectados a una tarjeta madre compatible solo con `BIOS/Legacy`, la segunda para tarjetas madre compatibles con ambas, `BIOS/Legacy` y `UEFI`, aunque lo recomendable es que para arranque `UEFI` se use siempre la tabla de particiones `GPT`.

Para consultar la tabla de particiones que tiene el dispositivo de almacenamiento donde va a instalar Arch Linux use el siguiente comando.

```shell
parted /dev/sda print | egrep "Model|msdos|gpt"
```

Por defecto cuando se inicia el live USB de Arch Linux en cualquiera de los modos `BIOS` o `UEFI`, las herramientas compatibles establecen automáticamente la tabla de partición más apropiada para el dispositivo; es decir, si inició el live USB en modo `BIOS`, entonces al usar por ejemplo la herramienta `cfdisk`, el disco duro aparecerá con la etiqueta dos, es decir `MBR`. En cambio, cuando se inicia el live USB en modo `UEFI`, la tabla de partición que se establece por defecto es `gpt`.

Si usted ve la necesidad de cambiar de una tabla de partición a otra, use una de las siguientes opciones. Tenga en cuenta que este procedimiento eliminará la información del dispositivo de almacenamiento escogido, en este caso `/dev/sda`:

##### Para convertir de `MBR` a `GPT`

```shell
parted /dev/sda mklabel gpt
```

##### Para convertir de `GPT` a `MBR`

```shell
parted /dev/sda mklabel msdos
```

#### Diseño de particiones y puntos de montaje

Las particiones son algo muy personal, pero si va a instalar Arch Linux como único sistema, para un usuario normal se recomienda usar cuatro particiones y en el siguiente orden.

1. Arranque.
2. Memoria de intercambio SWAP.
3. Raíz.
4. Usuario.

##### Modo BIOS Legacy

![BIOS/MBR diseño de ejemplo](/static/bios_mbr.png)

##### Modo UEFI/GPT

![UEFI/GPT diseño de ejemplo](/static/uefi_gpt.png)

##### Tamaño de la partición de intercambio (SWAP)

![SWAP](/static/swap.png)

Un caso especial al momento de crear las particiones es cuando vamos a instalar Arch Linux junto a Windows en modo `UEFI`. Para este específico caso solo tendríamos que crear tres particiones, omitiendo la partición de arranque, ya que el mismo Windows previamente instalado se encarga de crearla. Para los demás casos debemos crear las cuatro particiones normalmente.

#### Herramientas de particionado

Ahora que ya tiene la tabla de particiones correcta, el tamaño recomendado de las futuras particiones y el dispositivo de almacenamiento identificado, utilice una herramienta compatible con la tabla de partición del dispositivo de almacenamiento a particionar.

![Herramientas de particionado](/static/herramientas_particionado.png)

Verificamos las particiones creadas.

```shell
lsblk -po NAME,SIZE,FSTYPE /dev/sda
```

##### Formatear las particiones

Existen múltiples formatos de particiones disponibles para usar, sin embargo en esta guía usaremos `ext4`, ya que es el más usado y recomendado.

##### Partición de arranque modo BIOS/Legacy

```shell
mkfs.ext4 -F -L "boot" /dev/sda1
```

##### Partición de arranque modo UEFI

```shell
mkfs.fat -F 32 -n "UEFI" /dev/sda1
```

##### Partición de arranque modo Dual Boot

Si hará dual boot con Windows puede que desee etiquetar la partición `UEFI` asumiendo que es la partición `/dev/sda2` (que es lo más común).

```shell
fatlabel /dev/sda2 "uefi"
```

##### Partición de memoria virtual o memoria de intercambio SWAP

```shell
mkswap -L "SWAP" /dev/sda2
```

##### Partición raíz

```shell
mkfs.ext4 -F -L "root" /dev/sda3
```

##### Partición home

```shell
mkfs.ext4 -F -L "home" /dev/sda4
```

##### Verificar los formatos y las etiquetas de las particiones creadas

```shell
lsblk -po NAME,FSTYPE,LABEL,MOUNTPOINT,SIZE /dev/sda
```

#### Montar y activar las particiones

Para montar las particiones necesitamos crear antes sus rutas lógicas o puntos de montaje. Solo varía si va a instalar Arch Linux como único sistema en modo `BIOS` o `UEFI`. Para el primer caso el nombre de la ruta lógica del arranque debe nombrarse `/boot`, para el segundo `/efi`.

##### Activar partición SWAP

```shell
swapon /dev/sda2
```

##### Montamos la partición raíz

```shell
mount /dev/sda3 /mnt
```

##### Modo BIOS/Legacy

```shell
mkdir -p /mnt/{boot,home}/
```

##### Modo UEFI

```shell
mkdir -p /mnt/{efi,home}/
```

##### Verificamos que se hayan creado correctamente los directorios

```shell
ls /mnt
```

##### Si estamos en modo BIOS/Legacy montamos la partición boot

```shell
mount /dev/sda1 /mnt/boot
```

##### Si estamos en modo UEFI montamos la partición efi

```shell
mount /dev/sda1 /mnt/efi
```

##### Montamos la partición home

```shell
mount /dev/sda4 /mnt/home
```

##### Verificamos la información de las particiones

```shell
lsblk -po NAME,FSTYPE,LABEL,MOUNTPOINT /dev/sda
```

Debería tener en la columna `MOUNTPOINT` cuatro puntos de montaje, cada uno con su etiqueta (excepto /efi si tiene dual boot en modo `UEFI`) y su sistema de ficheros correspondiente.

### Configuración temporal de pacman

Teniendo formateadas y montadas las particiones, podemos ahora filtrar los mejores espejos o servidores de descarga.

#### Forma manual

Para este fin podemos manualmente abrir el fichero de configuración.

`/etc/pacman.d/mirrorlist`

Y con su editor favorito colocar en la cabeza de lista el o los servidores de su país y/o de su preferencia. Guarde para salvar los cambios.

#### Usando reflector

Otra opción es usar la herramienta reflector para filtrar los espejos más rápidos y sincronizados.

Filtramos los 20 mejores espejos o servidores de descarga.

```shell
reflector --verbose -l 20 --sort rate --save /etc/pacman.d/mirrorlist
```

#### Usando pacman mirrorlist generator

Si aun con las dos anteriores formas no te ha sido suficiente, entonces esta opción debería ser para ti. En esta ocasión vamos a necesitar de acceso a Internet para ingresar al siguiente enlace y así generar los espejos de descarga de tu país favorito con la herramienta online Pacman Mirrorlist Generator como lo ilustra la siguiente imagen.

![Pacman Mirrolist Generator](/static/mirrorlist.png)

Elija su país y en caso de de usar `IPv6` seleccione también el checkbox correspondiente.

Ahora presione el botón `Generate List` para generar la lista de servidores de descarga de su país preferido. Se generará la lista de mirrors habilitados y disponibles para su país. Deberá transcribirlos manualmente en el fichero de configuración de `pacman`.

#### Tips de mirrors

Mi recomendación personal y es lo que hago siempre al generar los mirrors es aprovechar la herramienta reflector para generar los mirrors más rápidos, y luego abro de nuevo el fichero `/etc/pacman.d/mirrorlist` y agrego los mirrors generados con la harramienta online Pacman Mirrorlist Generator correspondientes con mi país, en este caso Estados Unidos. Asegúrese de digitar correctamente el servidor para evitar errores al descargar paquetes.

### Tips de pacstrap

Algo que también recomiendo hacer antes de instalar el sistema, es configurar temporalmente `pacstrap` para que muestre el porcentaje de avance de descarga del total de los paquetes, esto nos permite estimar la duración de instalación de los paquetes. También podemos opcionalmente ponerle un poco de color a las salidas de `pacstrap` y hasta ponerle la animación del juego de pacman mientras se descargan dichos paquetes. Para ello abrimos el fichero de configuración de pacman `/etc/pacman.conf`.

```shell
vim /etc/pacman.conf
```

y luego descomentamos las siguientes líneas para que queden así.

```shell
Color
TotalDownload
```

Si se deciden activar la animación del emoji del juego pacman, vamos a agregar recomendablemente debajo de `#VerbosePkgLists` el siguiente comando.

```shell
ILoveCandy
```

### Instalación del sistema

#### Especificación de los paquetes principales

Esta es la lista de paquetes inicialmente necesarios para tener un sistema funcional.

| Categoría                       | Paquetes                             | Descripción                                                                                                    |
| ------------------------------- | ------------------------------------ | -------------------------------------------------------------------------------------------------------------- |
| Sistema base                    | `base` `base-devel`                  | Paquetes esenciales para un sistema funcional.                                                                 |
| Núcleos                         | `linux` `linux-lts`                  | Núcleos del sistema operativo.                                                                                 |
| Binarios de controladores       | `linux-firmware`                     | Binarios de controladores generalmente propietarios.                                                           |
| Gestor de arranque              | `grub`                               | Gestionar el arranque del sistema.                                                                             |
| Reconocimiento de OS            | `os-prober`                          | Reconocimiento de otros sistemas operativos instalados.                                                        |
| NTFS para Windows               | `ntfs-3g`                            | Permitir visualizar y gestionar particiones de Windows desde Arch Linux.                                       |
| UEFI Boot Manager               | `efibootmgr`                         | Necesario para instalación en modo UEFI.                                                                       |
| Conexiones de red               | `networkmanager` `dhcpcd`            | Gestionar conexiones a Internet cableadas y vía wifi.                                                          |
| Reconocimiento de dispositivos  | `gvfs-mtp` `gvfs-afc` `gvfs-gphoto2` | Reconocimiento de dispositivos de almacenamiento como memorias, smartphones Android/iPhone, cámaras digitales. |
| Editor de texto                 | `vim` o `nano`                       | Editor de texto para la línea de comandos.                                                                     |
| Sistema de control de versiones | `git`                                | Sistema de control de versiones para clonar paquetes o instalar desde el repositorio AUR.                      |

#### Modos de instalación

##### Único sistema

Instalando Arch Linux en modo `BIOS/Legacy` como único sistema.

```shell
pacstrap /mnt base base-devel linux linux-firmware grub networkmanager dhcpcd gvfs-mtp gvfs-afc gvfs-gphoto2 vim git
```

Instalando Arch Linux en modo `UEFI` como único sistema.

```shell
pacstrap /mnt base base-devel linux linux-firmware grub efibootmgr networkmanager dhcpcd gvfs-mtp gvfs-afc gvfs-gphoto2 vim git
```

##### Dual boot con Windows

Instalando Arch Linux en modo `BIOS/Legacy` dual boot con Windows.

```shell
pacstrap /mnt base base-devel linux linux-firmware grub os-prober ntfs-3g networkmanager dhcpcd gvfs-mtp gvfs-afc gvfs-gphoto2 vim git
```

Instalando Arch Linux en modo `UEFI` dual boot con Windows.

```shell
pacstrap /mnt base base-devel linux linux-firmware grub efibootmgr os-prober ntfs-3g networkmanager dhcpcd gvfs-mtp gvfs-afc gvfs-gphoto2 vim git
```

##### Error de `pacstrap`

Si sale un error al usar pacstrap del tipo:

```shell
error: could not open file /mnt/var/lib/pacman/sync/core.db: Unrecognized archive format
```

Tenemos que eliminar el directorio "...sync/" de forma recursiva para solucionarlo.

```shell
rm -R /mnt/var/lib/pacman/sync/
```

luego sí podemos usar pacstrap sin ningún problema.

#### Configuración del sistema

##### Fstab

Generar el fichero fstab, encargado de las particiones del dispositivo de almacenamiento donde se ha instalado Arch Linux.

```shell
genfstab -U /mnt >> /mnt/etc/fstab
```

Comprobamos que el fichero fue creado correctamente.

```shell
cat /mnt/etc/fstab
```

##### Chroot

Entramos a la raíz del nuevo sistema como usuario root.

```shell
arch-chroot /mnt
```

**NOTA:** Las configuraciones de ahora en adelante dejan de ser temporales y pasan a ser permanentes, por lo tanto repetiremos algunos de los comandos anteriormente usados.

##### Zona horaria

Ahora que estamos dentro del sistema recién instalado podemos establecer la zona horaria de acuerdo a nuestro país. Por ejemplo, si es de Estados Unidos, tan solo con agregar como última palabra al siguiente comando, el nombre de la capital de Estados Unidos en inglés, se mostrará la zona horaria que usted debe establecer.

```shell
timedatectl list-timezones | grep New_York
```

En este caso la zona horaria arrojada y que se debe establecer es `America/New_York`.

```shell
ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```

Otra forma fácil de consultar y establecer nuestra zona horaria al mismo tiempo.

```shell
ln -sf /usr/share/zoneinfo/$(curl -s https://ipapi.co/timezone) /etc/localtime
```

Establezca el reloj de hardware desde el reloj del sistema y actualice las marcas de tiempo en `/etc/adjtime`.

```shell
hwclock -w
```

Si emplea dual boot con Windows, la próxima ves que inicie Windows asegúrese deshabilitar el servicio de sincronización de hora por internet como lo explica el siguiente enlace.
Habilite e inicie el primer servicio de sincronización de red existente.

```shell
timedatectl set-ntp true
```

Establecer el reloj interno del computador (`RTC`) con la hora local, y así no tener problemas de sincronización de hora al iniciar cualquiera de los sistemas operativos.

```shell
timedatectl set-local-rtc 1
```

##### Localización

Establecemos la configuración regional, así que editamos el fichero `/etc/locale.gen` con VIM, descomentamos para este caso `es_US.UTF-8 UTF-8` y guardamos.

```shell
vim /etc/locale.gen
```

Generamos la configuración regional.

```shell
locale-gen
```

Establecemos la variable `LANG` con la configuración regional español Estados Unidos. Abrimos el archivo `/etc/locale.conf` con VIM.

```shell
LANG=es_US.UTF-8
```

Exportamos la variable `LANG`.

```shell
export LANG=es_US.UTF-8
```

Ahora verificamos los locale con el siguiente comando.

```shell
locale
```

Configuramos el mapa de teclado y la fuente de la terminal usado para la consola. Abrimos el archivo `/etc/vconsole.conf` con VIM y agregamos las siguientes lineas.

```shell
KEYMAP=la-latin1
FONT=Lat2-Terminus16
```

##### Configuración de red

Generamos el nombre del host para usar en la red.

```shell
echo "arch" > /etc/hostname
```

Configuramos el fichero hosts teniendo en cuenta su nombre de host anterior. Abrimos el archivo `/etc/hosts` con VIM y agregamos las siguientes lineas.

```shell
Static table lookup for hostnames.
See hosts(5) for details.

#

127.0.0.1 localhost
::1 localhost
127.0.1.1 arch.localdomain arch
```

##### `Initramfs`

Si usa `LVM`, `dm-crypt` o `RAID`, o realiza modificaciones al fichero `mkinitcpio.conf`, es necesario volver a cargar la imagen initranmfs, de lo contrario omita el siguiente comando, ya que al instalar el kernel con pacstrap se ha generado automáticamente la imagen del sistema.

```shell
mkinitcpio -P
```

##### Contraseña root

Establecemos la contraseña `root`.

```shell
passwd
```

Antes de instalar `grub` debemos estar seguros de haber generado la imagen `initramfs`.

```shell
ls /boot
```

#### Cargador de arranque `grub`

Una vez comprobada la existencia de la imagen de `linux`, ahora podemos generar el `grub`.

##### Grub BIOS Legacy

Si su sistema es `MBR` con `BIOS/Legacy` use el siguiente comando.

```shell
grub-install --target=i386-pc /dev/sda
```

##### Grub UEFI

En cambio si su sistema es `UEFI` el comando cambia un poco.

```shell
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB
```

No todas las placas admiten el anterior comando, así que puede usar el siguiente en caso dado no haya funcionado el anterior.

```shell
grub-install --target=x86_64-efi --efi-directory=/efi --removable
```

##### Arranque del sistema

Después de haber instalado el `grub` en cualquiera de los modos, es recomendable habilitar la visualización del arranque del sistema, eliminando la opción "`quiet`" de "`GRUB_CMDLINE_LINUX_DEFAULT`" en el fichero de configuración del `grub`.

Abrimos el archivo `/etc/default/grub` y agregamos la siguiente linea.

```shell
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3"
```

Por último generamos el fichero de configuración del `grub`.

```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

#### Usuarios y grupos

Ahora vamos a crear nuestro usuario con los grupos básicos necesarios, lo llamaremos "`usuario`".

```shell
useradd -m -g users -G audio,storage,video,wheel,lp,power,input -s /bin/bash usuario
```

Configuramos la contraseña del usuario creado.

```shell
passwd usuario
```

Nos toca ahora descomentar en el fichero `/etc/sudoers` la línea que indica el grupo `wheel` , el cual es el encargado de gestionar los permisos de superusuario de sus usuarios.

```shell
vim /etc/sudoers
```

y descomentamos la línea para que quede así.

```shell
%wheel ALL=(ALL) ALL
```

Escribimos en VIM el comando "`:wq!`" para aplicar los cambios y salir.

##### Directorios de usuarios

Instalamos el paquete que genera los diretorios de usuarios.

```shell
pacman -S xdg-user-dirs --needed --noconfirm
```

Generamos los directorios de los usuarios existentes, incluido `root`.

```shell
xdg-user-dirs-update
```

#### Configuración de pacman

Ahora configuraremos el gestor de paquetes de Arch Linux `pacman`. Lo primero que hacemos es abrir el fichero de configuración de pacman `/etc/pacman.conf`.

```shell
vim /etc/pacman.conf
```

##### Colores y porcentaje de avance en descarga de paquetes

Descomentar las líneas `Color` y `TotalDownload`, nos permite tener color en las salidas de pacman y mostrar el porcentaje de avance de descarga del total de los paquetes respectivamente.

```shell
Color
TotalDownload
```

##### Animación de pacman

Podemos activar la animación del emoji del juego pacman mientras se van descargando los paquetes, para eso vamos a agregar preferiblemente debajo de `#VerbosePkgLists` el siguiente comando.

```shell
ILoveCandy
```

##### Repositorio multilib

Para terminar la configuración de pacman es recomendable habilitar el repositorio `multilib`, el cual alberga paquetes de 32 bits que puede ser compilados y ejecutados en sistemas de 64 bits. Así que en el mismo fichero de configuración `/etc/pacman.conf` vamos un poco más abajo y descomentamos las líneas del repositorio.

```shell
[multilib]
Include = /etc/pacman.d/mirrorlist
```

Guarde para salvar los cambios.

#### Firmware del procesador

Requerimos antes de iniciar `xorg` el driver correcto de los gráficos internos y el paquete de actualizaciones del microcódigo del procesador. Así que vamos a instalar el paquete de actualizaciones de microcódigo de acuerdo a la marca de nuestro procesador, si no sabemos la marca de nuestro procesador podemos consultarlo con el siguiente comando.

```shell
less /proc/cpuinfo | grep CPU | uniq
```

o con este otro.

```shell
lscpu | grep modelo
```

Si su procesador es AMD instalamos.

```shell
pacman -Syu amd-ucode --noconfirm
```

Si su procesador es Intel instalamos.

```shell
pacman -Syu intel-ucode --noconfirm
```

#### Fuentes

Vamos a necesitar algunas fuentes, caracteres unicode y emojis.

```shell
pacman -S ttf-{dejavu,hack,roboto,liberation} wqy-microhei bdf-unifont unicode-character-database noto-fonts-emoji --needed --noconfirm
```

#### Servicios de red

Vamos ahora a habilitar los servicios de conexión a Internet.

Primero habilitamos `networkmanager` y luego el `dhcpcd`.

```shell
systemctl enable NetworkManager.service
```

```shell
systemctl enable dhcpcd.service
```

#### Reiniciar

Ahora que hemos terminado de instalar Arch Linux puro, debemos salir del entorno `chroot`.

```shell
exit
```

Luego desactivamos la memoria de intercambio SWAP.

```shell
swapoff -a
```

Desmontamos los puntos de montaje con un solo comando.

```shell
umount -R /mnt
```

Ahora sí procedemos a reiniciar el sistema.

```shell
reboot
```

#### Solución de problemas

Teniendo instalado el `grub` en cualquiera de los modos `BIOS/Legacy` o `UEFI`, vamos a eliminar el bug que sale justo antes de iniciar el GRUB.

```shell
error: file 'boot/grub/locale/es.gmo' not found
```

Para eso vamos a verificar la existencia del directorio `/boot/grub/locale/`.

```shell
ls /boot/grub/locale
```

Si no existe el directorio lo creamos.

```shell
mkdir -p /boot/grub/locale/
```

Bien, ahora copiamos el fichero `grub.mo` del directorio locale del idioma del sistema, en el directorio `/usr/share/locale/` creado en `grub`.

```shell
cp /usr/share/locale/$(echo $LANG | cut -d "\_" -f1)/LC_MESSAGES/grub.mo /boot/grub/locale/es.gmo
```

#### Referencias

20 April 2020. Installation Guide. [https://wiki.archlinux.org/index.php/Installation_guide](https://wiki.archlinux.org/index.php/Installation_guide)

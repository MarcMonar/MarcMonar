# Redundancia de Discos: Implementación RAID 10 con mdadm

## Introducción

Un administrador de sistemas debe considerar la posibilidad de fallos y pérdida de equipos. En este escenario, se utilizará e implementará el sistema RAID para asegurar la disponibilidad y redundancia del servidor. La práctica se centrará en la implementación de un RAID 10 gestionado con volúmenes lógicos utilizando mdadm.

### Desarrollo

1. **Creación de Grupos de Volúmenes:**

   Comprobamos y detectamos los cuatro volúmenes de 2 GB:

   ```bash
   $ fdisk -l | grep -E "/dev/sd?"
   ```

   Resultado:

   ```
   Disco /dev/sdb: 2 GiB
   Disco /dev/sdc: 2 GiB
   Disco /dev/sdd: 2 GiB
   Disco /dev/sde: 2 GiB
   ```

2. **Instalación de mdadm:**

   ```bash
   $ sudo apt install mdadm -y
   ```

3. **Creación de RAID 10:**

   Nombres de los conjuntos RAID:

   - `md0`: Con los discos `sdb` y `sdc`
   - `md1`: Con los discos `sdd` y `sde`
   - `md2`: RAID 0 con la creación de `md0` y `md1`

   Creación de los conjuntos RAID:

   ```bash
   mdadm --create /dev/md0 --level=1 --raid-disks 2 /dev/sdb /dev/sdc
   mdadm --create /dev/md1 --level=1 --raid-disks 2 /dev/sdd /dev/sde
   mdadm --create /dev/md2 --level=0 --raid-disks=2 /dev/md0 /dev/md1
   ```

4. **Verificación de Funcionamiento:**

   Comprobamos que los conjuntos RAID han sido creados correctamente:

   ```bash
   mdadm --detail /dev/md0
   mdadm --detail /dev/md1
   mdadm --detail /dev/md2
   ```

   Añadimos las líneas de detalle al archivo `/etc/mdadm/mdadm.conf`:

   ```bash
   mdadm --detail --scan >> /etc/mdadm/mdadm.conf
   ```

5. **Creación de Archivos en `md2`:**

   Montamos el disco duro `md2` para añadir archivos en formato ext4:

   ```bash
   mkfs.ext4 /dev/md2
   mkdir /archivos_raid
   mount /dev/md2 /archivos_raid
   ```

   Añadimos información a la carpeta montada:

   ```bash
   touch "Prueba" /archivos_raid
   ```

   Añadimos la entrada al archivo `/etc/fstab` para persistencia:

   ```bash
   vim /etc/fstab
   /dev/md2 /archivos_raid ext4 defaults 0 1
   ```

6. **Creación de Discos Duros de Hot Spare:**

   Verificamos la creación de los discos:

   ```bash
   fdisk -l
   ```

7. **Comprobación de Cambios en RAID:**

   Comprobamos que no haya cambios antes de proceder:

   ```bash
   mdadm --detail /dev/md{0,1}
   ```

   Creamos un archivo en la carpeta `archivos_raid` y comprobamos con `md5sum`:

   ```bash
   cd /archivos_raid/
   sudo dd if=/dev/zero of=archivo bs=1M count=500
   ls
   md5sum archivo > signature.md5sum
   ```

   Cambiamos los discos y comprobamos nuevamente:

   ```bash
   mdadm --manage /dev/md0 --fail /dev/sdc
   mdadm --manage /dev/md1 --fail /dev/sde
   mdadm --manage /dev/md0 --remove /dev/sdc
   mdadm --manage /dev/md1 --remove /dev/sde
   mdadm --manage /dev/md0 --add /dev/sdf
   mdadm --manage /dev/md1 --add /dev/sdg
   ```

8. **Verificación Final:**

   Comprobamos que todo haya salido bien:

   ```bash
   md5sum archivo > signature2.md5sum
   if [ "$(cat signature2.md5sum)" == "$(cat signature.md5sum)" ]; then echo -e "Todo bien"; else echo -e "Algo ha fallado"; fi
   ```

9. **Desmontaje y Creación de LVM:**

   Desmontamos todo del `fstab` y desmontamos `md1` para comenzar a crear particiones dentro de él:

   ```bash
   shred /dev/md1
   umount /dev/md1
   swapoff -a
   reboot
   ```

## Creación de LVM

1. **Creación del Disco Físico:**

   Creamos el disco "físico" `/dev/md1`:

   ```bash
   pvcreate /dev/md1
   ```

   Verificamos:

   ```bash
   pvscan
   ```

2. **Particiones y Formateo:**

   Creamos el grupo de volúmenes, la partición de 95%, y formateamos:

   ```bash
   vgcreate vgmd1 /dev/md1
   lvcreate -n part1 -L 1900MB vgmd1
   sudo vgdisplay vgmd1
   mkfs.ext4 /dev/vgmd1/part1
   ```

3. **Ajuste del Tamaño:**

   Ajustamos el tamaño de la partición al 60%:

   ```bash
   lvreduce -L -600M /dev/vgmd1/part1
   ```

   Confirmamos y verificamos:

   ```bash
   Do you really want to reduce vgmd1/part1? [y/n]: yes
   ```

   Verificamos:

   ```bash
   sudo df -h /dev/vgmd1/part1
   ```

Este procedimiento garantiza un nivel adicional de seguridad y redundancia en caso de pérdida de datos.

# rpi
raspiberry pi stuff


NFS :

- Install raspian to sd card (via NOOBS or sd image soft like dd, or winimage32) 
- Boot up let reaspian do it's thing (resize boot partition etc)
- Install nfs-common
- Mount nfs location and copy rootfs to nfs
- edit /boot/cmline.txt 

# this is the bare minimum needed to boot from nfsroot
    dwc_otg.lpm_enable=0 console=tty3 initrd=initrd.img.nbd root=/dev/nfs nfsroot=192.168.121.40:/nfs/pi/disk,tcp,vers=4 rw ip=dhcp rootfstype=nfs elevator=deadline rootwait smsc95xx.turbo_mode=N

- before booting edit you fstab comment the / mount line

proc            /proc           proc    defaults          0       0
/dev/mmcblk0p1  /boot           vfat    defaults          0       2
#/dev/mmcblk0p7  /               ext4    defaults,noatime  0       1

I didn't need to add the nfs in fstab since it's passed as a command at boot






NBD : WIP

- same steps but instead of a directory we need to create a sparse image to boot on

      truncate -s 20000M file.img
      cp file.img raspi_fs.img
- change user:group for nbd 
      sudo chown nbd:nbd raspi_fs.img
- sudo modrprobe nbd
- Mount nbd location and copy rootfs to nbd
     nbd-client -N raspi /dev/nbd0
- edit /boot/cmline.txt       


      dwc_otg.lpm_enable=0 console=tty3 initrd=initrd.img.nbd nbdroot=192.168.121.40,raspi,nbd0 root=/dev/nbd0 rootfstype=btrfs ip=dhcp fsck.repair=yes elevator=deadline rootwait smsc95xx.turbo_mode=N
      
 _ this is not working _
 
 wip : try to compile vmlinuz and initrd to boot from
 
 default kernel.img seem to load nbd module just i get stuck at waiting for root /dev/nbd0
 
 tried multiple variations of cmdline.txt all result the same

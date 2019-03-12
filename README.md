# Autoimport all STL and GCODE files to Octoprint

 1. Install `usbmount` with `sudo apt-get install usbmount`
 2. Install NTFS driver package **ntfs-3g** with `sudo apt-get install ntfs-3g`
 3. Configure usbmount to mount specified filesystems by opening the usbmount file with `sudo nano /etc/usbmount/usbmount.conf` 
 4. Change line called `FILESYSTEMS=""` to `FILESYSTEMS="vfat ntfs fuseblk ext2 ext3 ext4 hfsplus"` so all filesystems are recognized.
 5. Change line called `FS_MOUNTOPTIONS=""` to `FS_MOUNTOPTIONS="-fstype=ntfs-3g,nls=utf8,umask=007,gid=46 -fstype=fuseblk,nls=utf8,umask=007,gid=46 -fstype=vfat,gid=1000,uid=1000,umask=007"`
 6. Create the file **usbmount.rules** in **/etc/udev/rules.d/** with `sudo nano /etc/udev/rules.d/usbmount.rules`
 7. Paste this: ```KERNEL=="sd*", DRIVERS=="sbp2",         ACTION=="add",  PROGRAM="/bin/systemd-escape -p --template=usbmount@.service $env{DEVNAME}", ENV{SYSTEMD_WANTS}+="%c"
KERNEL=="sd*", SUBSYSTEMS=="usb",       ACTION=="add",  PROGRAM="/bin/systemd-escape -p --template=usbmount@.service $env{DEVNAME}", ENV{SYSTEMD_WANTS}+="%c"
KERNEL=="ub*", SUBSYSTEMS=="usb",       ACTION=="add",  PROGRAM="/bin/systemd-escape -p --template=usbmount@.service $env{DEVNAME}", ENV{SYSTEMD_WANTS}+="%c"
KERNEL=="sd*",                          ACTION=="remove",       RUN+="/usr/share/usbmount/usbmount remove"
KERNEL=="ub*",                          ACTION=="remove",       RUN+="/usr/share/usbmount/usbmount remove"``` and save
 8. Create the file **usbmount@.service** in **/etc/systemd/system/** with `sudo nano /etc/systemd/system/usbmount@.service`
 9. Paste this: ```[Unit]
BindTo=%i.device
After=%i.device
[Service]
Type=oneshot
TimeoutStartSec=0
Environment=DEVNAME=%I
ExecStart=/usr/share/usbmount/usbmount add
RemainAfterExit=yes``` and save
 10. Create the move script with `nano /home/pi/scripts/movefiles` 
 11. Paste this: ```cp /media/usb0/*.stl /home/pi/.octoprint/uploads/USB/
   cp /media/usb0/*.gcode /home/pi/.octoprint/uploads/USB/ ```
 12. Create UDEV rule with `sudo nano /etc/udev/rules.d/85-my_rule.rules`
 13. Add `ACTION=="add", SUBSYSTEM=="usb_device",RUN+="usr/bin/local/script.sh"` and save
 14. Reboot. `sudo reboot`






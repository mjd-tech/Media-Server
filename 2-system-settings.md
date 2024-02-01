# System settings
If you have a 4k TV, the first thing you notice is 
the text in the Login screen is way too small.

## Login Screen
edit (as root) `/etc/lightdm/lightdm.conf`,
add "display-setup-script" line immediately after `[Seat:*]`
```
[Seat:*]
display-setup-script=xrandr -s 1920x1080
...
```
## Gui Control Center

These are the settings I use:
- Display: 1920x1080 60Hz
- Startup Apps: disable all except Power Manager, SSH Key Agent
- Power Management: never sleep
- Screensaver: disable
- Appearance: Theme - Mint-Y-Dark. Customize: Window Border: Splint
- Pointer: set large mouse pointer
- Fonts: set to size 16
- Desktop settings...Windows...Window Manager: Buttons on left.
- Users and Groups: User settings...Password...Don't ask for Password on login

## Kernel
- see this https://jellyfin.org/docs/general/administration/hardware-acceleration/intel#linux-setups
- Go into gui Update Manager, install kernel 6.2

## Low Power Encoding
- JasperLake GPU needs "Low Power Encoding"
- see: https://jellyfin.org/docs/general/administration/hardware-acceleration/intel#configure-and-verify-lp-mode-on-linux
- Add the required i915 kernel parameter on the host system to enable loading GuC and HuC firmware

```
sudo sh -c "echo 'options i915 enable_guc=2' >> /etc/modprobe.d/i915.conf"
sudo update-initramfs -u && sudo update-grub
```
Note:
When I did this I got a warning:
```
update-initramfs: Generating /boot/initrd.img-6.2.0-26-generic
W: Possible missing firmware /lib/firmware/i915/dg2_huc_gsc.bin for module i915
```
For some reason, the "linux-firmware" package was missing `dg2_huc_gsc.bin`.
I had to get the dg2_huc_gsc.bin file from kernel.org git repository.
This problem is fixed as of linux-firmware (20220329.git681281e4-0ubuntu3.18)

Reboot the system and check the GuC & HuC status with the following commands,
make sure there is no FAIL or ERROR in the outputs.

```
sudo reboot
sudo dmesg | grep i915
sudo cat /sys/kernel/debug/dri/0/gt/uc/guc_info
sudo cat /sys/kernel/debug/dri/0/gt/uc/huc_info
```

## Intel Video driver

> Don't do this section unless you have "judder" problems

- by default, the xorg "modesetting" driver is used
- this produces "judder" in Kodi, frames are occasionally dropped.
- see [Kodi 19.4 on Xubuntu J4125 24p judder](https://forum.kodi.tv/showthread.php?tid=367862&pid=3094414)
- and [Solved -  Intel driver issue on following mainboard?](https://forum.kodi.tv/showthread.php?tid=371763&pid=3138139#pid3138139)
- the recommendation is to use the older intel driver

```
# Intel driver was already installed and /etc/X11/xorg.conf.d/ already exists
#sudo apt install xserver-xorg-video-intel
#sudo mkdir -p /etc/X11/xorg.conf.d
cd /etc/X11/xorg.conf.d/
sudo ln -s /usr/share/doc/xserver-xorg-video-intel/xorg.conf 10-intel.conf
```

## Reduce unnecessary writes to ssd
- edit (as root) `/etc/fstab`: add noatime in / mount options. Comment swapfile line.

For example:
```
# / was on /dev/nvme0n1p2 during installation
UUID=bac86fbe-0eb0-4bc5-b14f-e3355a72ab13 /               ext4    noatime,errors=remount-ro 0       1
# /boot/efi was on /dev/nvme0n1p1 during installation
UUID=AB5C-2B6E  /boot/efi       vfat    umask=0077      0       1
# /swapfile                                 none            swap    sw              0       0
```

- delete (as root) /swapfile
- /tmp in ram: `sudo systemctl enable /usr/share/systemd/tmp.mount`
- install log2ram: https://github.com/azlux/log2ram/blob/master/log2ram

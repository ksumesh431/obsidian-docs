
trixie location
https://cdimage.debian.org/cdimage/trixie_di_rc1/amd64/iso-dvd/

---

# nvidia driver install

install linux headers
`sudo apt install linux-headers-$(uname -r)`
then
#### Debian

#### Testing "Trixie"

##### Version 535.216.03

For support of GeForce 700 series and newer GPUs. For older devices, you must use [nouveau](https://nouveau.freedesktop.org/), which should be already installed and in use.

1. Add "contrib", "non-free" and "non-free-firmware" components to /etc/apt/sources.list, for example:
    
    deb http://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware
    
2. If you have **[SecureBoot](https://wiki.debian.org/SecureBoot)** enabled: you need to enroll your machine owner's key (MOK) to use DKMS modules. Detailed instructions are available [here](https://wiki.debian.org/SecureBoot#DKMS_and_secure_boot). It's recommended to do this before installing nvidia-driver so that you do not have to rebuild the kernel image.
    
3. If your system uses dracut, you must tell dracut to include modprobe configuration files:
    
    - Make a dracut configuration file /etc/dracut.conf.d/10-nvidia.conf (you can actually name it anything you like, as long as it ends in .conf) with the following contents:
        
        install_items+=" /etc/modprobe.d/nvidia-blacklists-nouveau.conf /etc/modprobe.d/nvidia.conf /etc/modprobe.d/nvidia-options.conf "
        
    - Note the spaces between quotes and characters.
    - The modprobe.d files referenced here will be added by the nvidia-driver package.
        
4. Update the list of available packages:
    
    # apt update
    
5. To install "proprietary" flavor, packages [nvidia-kernel-dkms](https://packages.debian.org/nvidia-kernel-dkms "DebianPkg"), [nvidia-driver](https://packages.debian.org/nvidia-driver "DebianPkg") and [firmware-misc-nonfree](https://packages.debian.org/firmware-misc-nonfree "DebianPkg") should be installed:
    
    # apt install nvidia-kernel-dkms nvidia-driver firmware-misc-nonfree
    
    To install "open" flavor, replace [nvidia-kernel-dkms](https://packages.debian.org/nvidia-kernel-dkms "DebianPkg") with [nvidia-open-kernel-dkms](https://packages.debian.org/nvidia-open-kernel-dkms "DebianPkg"):
    
    # apt install nvidia-open-kernel-dkms nvidia-driver firmware-misc-nonfree
    
    DKMS will build the nvidia module for your system, via the [nvidia-kernel-dkms](https://packages.debian.org/nvidia-kernel-dkms "DebianPkg") or [nvidia-open-kernel-dkms](https://packages.debian.org/nvidia-open-kernel-dkms "DebianPkg") package.
    
6. Restart your system to load the new driver.
7. If you plan to use wayland desktop (which is a default since Debian 12 "Bookworm") you may need to check and correct the following:
    
    - Check if the following command returns Y:
        
        # cat /sys/module/nvidia_drm/parameters/modeset
        
    - If this command returns N you need to set nvidia-drm modeset by adding options nvidia-drm modeset=1 to /etc/modprobe.d/nvidia-options.conf. For example:
        
        # echo "options nvidia-drm modeset=1" >> /etc/modprobe.d/nvidia-options.conf
        
    - If you plan to use suspend/hibernate functionality under KDE desktop environment, you may want to add another option to avoid graphics "glitches" after wakeup/restore:
        
        **Warning**: skip this step if you have Optimus hybrid graphics
        
        # echo "options nvidia NVreg_PreserveVideoMemoryAllocations=1" >> /etc/modprobe.d/nvidia-options.conf
        
    - Then reboot your system (check again if modeset is properly set, as described above) and Nvidia card should be properly recognized and used under wayland desktop.

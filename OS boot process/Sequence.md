
### LINUX 

POST (power on self test)>
 bootloder is initialsised (bios or uefi) >
 bootloader loads the kernel >
 init/ systemd process started >
 after that init.d or systemd processes are starteed >
 and after loading the various basic services and daemons, all file systems are mounted form fstab >
 then we get the user login prompt


### WINDOWS 
POST (power on self test)>
 bootloader initialised (bios or uefi) >
 windows bnoot manager now looks for windows os loader >
 windows os loader initialises drivers, windows kerneland other critical components into the ram >
 after kernel is initialised, the registry is loaded >
 file systems are mounted and essential services like active directory etc are loaded >
 then we reach the user login
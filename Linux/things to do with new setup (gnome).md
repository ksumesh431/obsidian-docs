install nvidia drivers
setup flatpak and install obsidian
setup git and download this obsidian repo

install extension manager flatpak
install extensions (make sure to have list or make git repo to sync)

set en us utf 8 locale (for btop and nerd fonts)
``` bash
sudo dpkg-reconfigiure locales

# then select the en us utf 8 locale. remove any other and log out

```

alias notes='nohup gnome-text-editor ~/notes > /dev/null 2>&1 &' for notes

alias win='virsh --connect qemu:///system domstate win11micro | grep -q "running" || virsh --connect qemu:///system start win11micro; virt-manager --connect qemu:///system --show-domain-console win11micro'
for virt manager win 11

packages to install
- telegram
- fastfetch
- btop
- vscode 
- oh my posh terminal setup
-  
zen backup and restore in new stystem


FIX OBS

copy aliasesas
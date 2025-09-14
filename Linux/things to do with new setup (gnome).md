
use x11, change password
/etc/pam.d/common-password, set password   requisite    pam_unix.so nullok yescrypt minlen=1
Disable suspend on plug in , dark mode
Open terminal , setup win+enter to open and cntr+w to close terminal
set nocpmpatible in ~/.vimrc and ~/.bash_aliases cls, ll and install vim
Setup apt repos if not setup and do apt update and full-upgrade
Install git , clone and run https://github.com/ksumesh431/local_ansible_debian.git
Login to google in firefox, setup t3 chat
create task mfor download and install of vs code from t3. going forward, use it to edit play
after installing vs code through ansible, open vs code  using 
`sudo code /root/ansible-laptop --no-sandbox --user-data-dir /tmp`

---

















install nvidia drivers
setup flatpak and install obsidian


install extension manager flatpak
install extensions (make sure to have list or make git repo to sync)

setup git (ssh) and download this obsidian repo

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
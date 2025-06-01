##### 1. Install qemu

`apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils`

##### 2. check KVM CPU support

`kvm-ok`

The output should look as following:

```
INFO: /dev/kvm exists
KVM acceleration can be used
```

You should reboot your system now to make sure everything goes smoothly.

##### 3. install and launch virt-manager

We will use `virt-manager` as GUI frontend for controlling our virtual machines. Install it as following:

`apt install virt-manager`

And launch it with `virt-manager`

It will ask you for your sudo password then.

##### 4. create virtual machine

After you've started `virt-manager`, simply click on the button with the green frame to start the VM creation wizard.


##### 5. Install spice guest tools on windows
https://www.spice-space.org/download.html


##### 6. Install spice folder sharing tool
![](Images/Pasted%20image%2020250120040613.png)


![](Images/Pasted%20image%2020250120040821.png)

![](Images/Pasted%20image%2020250120040851.png)

![](Images/Pasted%20image%2020250120040916.png)

![](Images/Pasted%20image%2020250120040943.png)

![](Images/Pasted%20image%2020250120041014.png)

![](Images/Pasted%20image%2020250120041057.png)


#### Alias for opening
`alias win='nohup bash -c '\''virsh domstate win10 | grep -q running || virsh start win10 && virt-viewer win10'\'' > /dev/null 2>&1 &'`





---
---

# Win 11
install and setup kvm, then follow these steps
1. iso using microwin
2. https://sysguides.com/install-a-windows-11-virtual-machine-on-kvm
3. chris util to debloat
4. activate
5. do not use core isolation due to significant performace hit

For file share, share the folder using smb. follow these steps
Create a folder in windows, and go to properties
![](Images/Pasted%20image%2020250216013137.png)

go to Share and put add "Everyone" with read and write
![](Images/Pasted%20image%2020250216013217.png)



Then search for managed advanced sharing
![](Images/Pasted%20image%2020250216013306.png)

Turn off password protected sharing
![](Images/Pasted%20image%2020250216013348.png)

==navigate to Settings > System > About, then click "Rename this PC  to change to a better hostname like win11

also in the folder properties under sharing, under advanced sharing, tick use this folder to get direct share path

### NEW SETUP FOR DEBIAN SMB FILESHARE
``` 
Part 1: Windows 11 VM (Guest) Configuration
1. Create the Folder to Share:

On your Windows 11 VM, create a folder you want to share (e.g., C:\Hostshare).
You can put some test files in it.
2. Set Network Profile to Private:

Go to Windows Settings.
Navigate to Network & internet.
Click on your active network connection (e.g., "Ethernet").
Under "Network profile type," select Private.
3. Configure Folder Sharing and Permissions:

Right-click on the folder you created (e.g., C:\Hostshare).
Select Properties.
Go to the Sharing tab.
Click Advanced Sharing....

Check the box Share this folder.
You can keep the default "Share name" or change it if desired (we used Hostshare).
Click the Permissions button.

Select Everyone in the list.
Under "Permissions for Everyone," check Allow for Full Control (or at least "Change" and "Read" if you prefer more restricted access from Linux).
Click Apply, then OK.


Click Apply, then OK in the Advanced Sharing window.


4. Configure NTFS Security Permissions:

In the folder's Properties window, go to the Security tab.
You need to ensure "Everyone" or the "Guest" account has appropriate permissions here too.

Click Edit....
If Everyone is not in the "Group or user names" list, click Add....

Type Everyone and click Check Names.
Click OK.


Select Everyone from the list.
In the "Permissions for Everyone" box below, check Allow for Full Control (or at least "Modify", "Read & execute", "List folder contents", "Read", and "Write").
Click Apply, then OK.


Click Close on the Properties window.
5. Disable Password Protected Sharing:

Open Control Panel (you can search for it).
Go to Network and Sharing Center.
On the left, click Change advanced sharing settings.
Expand the All Networks section.
Under "Password protected sharing," select Turn off password protected sharing.
Click Save changes.
6. Enable File and Printer Sharing for Private Networks:

While still in "Advanced sharing settings," expand the Private (current profile) section.
Ensure Turn on network discovery is selected (and its sub-option "Turn on automatic setup of network connected devices").
Ensure Turn on file and printer sharing is selected.
Click Save changes if you made any.
7. (Optional but Recommended) Note the VM's IP Address:

Open Command Prompt or PowerShell in the Windows 11 VM.
Type ipconfig and note the IPv4 address (e.g., 192.168.122.225). This is useful for the Debian setup.
Part 2: Debian Host Configuration
1. Install CIFS Utilities:

Open a terminal on your Debian host.
Update your package list and install cifs-utils:
bash      sudo apt update      sudo apt install cifs-utils -y      
2. Create a Local Mount Point:

This is an empty directory where the Windows share will be accessible.
bash      sudo mkdir -p /mnt/win11share       
(You can choose a different path like /media/your_username/win11share if you prefer, just be consistent).
3. Determine Your User ID (UID) and Group ID (GID):

You'll need these numeric IDs to ensure you own the files in the mounted share.
bash      id      
Look for uid=XXXX(yourusername) and gid=YYYY(yourgroupname). Note down the numbers XXXX and YYYY (e.g., 1000).
4. Configure /etc/fstab for Auto-Mounting on Access:

Open the /etc/fstab file for editing with root privileges:
bash      sudo nano /etc/fstab      
Go to the end of the file and add a new line. Replace WINDOWS_VM_IP with the actual IP address of your Windows VM (e.g., 192.168.122.225), and YOUR_UID and YOUR_GID with the numeric IDs you found in the previous step.
text

```

Now from linux host, go to smb://windows_hostname/shared_folder
Can add to bookmark

ALso must set vgamem="65536" in Video settings. with this, can scale to 4k
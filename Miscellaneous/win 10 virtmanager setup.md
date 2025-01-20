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
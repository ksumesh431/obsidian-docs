
To get available groups
`getent group`


<br/>
<br/>

---

---
<br/>
<br/>


# To create a group
`sudo groupadd testgroupname`

<br/>
<br/>
<br/>


# To create a user and add it to the group
`sudo useradd -G testgroupname -m testusername`

Set password using `sudo passwd testusername`

**OR**

**NEW METHOD**

### To create a new user
`sudo adduser username`


<br/>

### To add it to the group
`sudo adduser username groupname`


<br/>
<br/>
<br/>

# For modifying the sudoers file

`sudo visudo`

Add the follwing line to make users of this group able to sudo to "oracle" user passwordless.

`testgroupname ALL=(oracle) NOPASSWD: /bin/su - oracle `



<br/>
<br/>
<br/>


# To expire the password, so that user will put new password when logging in for the 1st time

`sudo passwd -e username`
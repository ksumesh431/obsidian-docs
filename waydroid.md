``` 
links:
waydroid: https://waydro.id/
waydroid scrpit: https://github.com/casualsnek/waydroid_script

firewal fix:
sudo ufw default allow FORWARD
sudo ufw allow 53
sudo ufw allow 67

weston and waydroid commands:
weston --socket=$WAYLAND_DISPLAY --backend=x11-backend.so --width=486 --height=1000
waydroid session start
waydroid show-full-ui
waydroid session stop
waydroid app install <apkFile>

comands needed to setup startAndroid
touch
chown 755
chmod 0:0
```


startAndroid
``` 
#!/bin/bash
export WAYLAND_DISPLAY=mysocket
weston --socket=$WAYLAND_DISPLAY --backend=x11-backend.so --width=486 --height=1000 &
waydroid show-full-ui &
```
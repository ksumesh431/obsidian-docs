1.  Create a new systemd service unit file in the directory /etc/systemd/system/ with a name of your choice, such as myservice.service.
    
2.  Inside the unit file, specify the service properties such as the user and group to run the service under, the working directory, and the command to start the service.
    
3.  Use the systemctl command to start, stop, and check the status of the service.
    
4.  To enable the service to automatically start at boot, use the systemctl enable command.


Here's an example of a systemd service unit file for a program called myprogram:
``` 
[Unit]
Description=My Program Service

[Service]
User=myuser
Group=mygroup
WorkingDirectory=/path/to/myprogram
ExecStart=/path/to/myprogram/myprogram
Restart=always

[Install]
WantedBy=multi-user.target
```

You can then start the service using `sudo systemctl start myservice.service`, stop it using `sudo systemctl stop myservice.service`, check the status with `sudo systemctl status myservice.service` and enable it to start at boot using `sudo systemctl enable myservice.service`.
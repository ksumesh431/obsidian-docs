
`kill -SIGSTOP $(pgrep *processName*)`
kill with sigstop signal sends the stop signal to the process so it is **Paused** so that its state can be resumed later.

`-SIGCONT` resumes it
`-SIGTERM` makes the process shut down normally
`-SIGKILL` kills the process immidiately

*pgrep gets the PID of the process listed


<br/>


## To view services 
`ps -aux `


<br/>
<br/>

---
---


<br/>
<br/>



# To give 400 to windows pem file

If you prefer to do it from UI

-   select .pem file -> right click -> properties
-   Security > Advanced > Disable inheritance
-   Remove all Users
-   Add > Select a principal
-   In "Enter the object name to select" type your Windows username > ok
-   Give all permissions > ok > apply

## Wont work with wsl terminal 






<br/>
<br/>

---
---


<br/>
<br/>



# To change to a specific user

`su - username`

`sudo su - ` for switching to root user

The - starts a new login shell with new environment variables and aliases etc.



<br/>
<br/>

---
---


<br/>
<br/>


# To run commands as another user

`sudo -u username command`

or to run commands as root user
`sudo command`
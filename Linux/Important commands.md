
`kill -SIGSTOP $(pgrep *processName*)`
kill with sigstop signal sends the stop signal to the process so it is **Paused** so that its state can be resumed later.

`-SIGCONT` resumes it
`-SIGTERM` makes the process shut down normally
`-SIGKILL` kills the process immidiately

*pgrep gets the PID of the process listed


<br/>


## To view services 
`ps -aux `



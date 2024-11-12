in case of errors liek
```
[timestamp] hostname systemd[1]: servicename.service: Service hold-off time over, scheduling restart.  
[timestamp] hostname systemd[1]: Stopped [Service description]
[timestamp] hostname systemd[1]: servicename.service: Start request repeated too quickly.  
[timestamp] hostname systemd[1]: Failed to start [Service description]
[timestamp] hostname systemd[1]: servicename.service: Unit entered failed state.  
[timestamp] hostname systemd[1]: servicename.service: Failed with result 'start-limit-hit'.
```
#startLimitHit #repeatedTooQuickly

`systemctl reset-failed servicename.service`


---

# In case of ec2 instance without permanant elastic ip

`cd /var/lib/jenkins/`

Modify jenkins.model.JenkinsLocationConfiguration.xml file by executing below command:
`sudo vi jenkins.model.JenkinsLocationConfiguration.xml`

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzyX11Vv4Nc9vFUt8NHbc49esB3cqYvOc_yWM_PLzNeCKfDW8R5GPXR8f0ECMsi_T1mC54DbkKz-8Q4oyC-FylNT8BUon75lz13tEC6dM9dzJbdgkDVPAiTQbHGhOKUuchTqTV9Zz5GsLNyDAN2CsXLgZieGhehyphenhyphenzRDU-ZSq5eUzta0tVMTor5cQaBJi4/s320/Screenshot%202024-04-20%20at%208.34.17%20PM.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzyX11Vv4Nc9vFUt8NHbc49esB3cqYvOc_yWM_PLzNeCKfDW8R5GPXR8f0ECMsi_T1mC54DbkKz-8Q4oyC-FylNT8BUon75lz13tEC6dM9dzJbdgkDVPAiTQbHGhOKUuchTqTV9Zz5GsLNyDAN2CsXLgZieGhehyphenhyphenzRDU-ZSq5eUzta0tVMTor5cQaBJi4/s895/Screenshot%202024-04-20%20at%208.34.17%20PM.png)

Make sure you provide Jenkins current URL (new ip or dns) in below location and restart Jenkins.
`sudo service jenkins restart`
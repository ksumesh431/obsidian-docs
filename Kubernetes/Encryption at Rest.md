
# Ref: https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/

<br/>
<br/>

---

---
<br/>
<br/>




## Create a encryption config file
![](Images/Pasted%20image%2020230212223146.png)


<br/>


Genrate a key and put in the Secrets field of encryption config
![](Images/Pasted%20image%2020230212223215.png)



<br/>



Edit the kube-apiserver config file. 
- set the --encryption-provider-config flag
- mount the encryption config file on the kube-apiserver static pod

![](Images/Pasted%20image%2020230212223418.png)




<br/>


![](Images/Pasted%20image%2020230212223616.png)










<br/>
<br/>

---

---
<br/>
<br/>



# Verify encryption using
![](Images/Pasted%20image%2020230212223528.png)

The output should not shoo the secret1 values in plain text.
# To monitor pods status live 
`kubectl get pods --watch`

<br/>
<br/>

---

---
<br/>
<br/>


# To get yaml output for a command without executing 
`kubectl command --dry-run=client -o yaml`

`kubectl get pod podName -o yaml` to get yaml output (MUST USE GET NOT DESCRIBE)


<br/>
<br/>

---

---
<br/>
<br/>

# To show objects in all namespaces

`--namespace=all`


<br/>
<br/>

---

---
<br/>
<br/>


![](Images/Pasted%20image%2020230212035029.png)


<br/>
<br/>

---

---
<br/>
<br/>


# To force replace a resource , by deleting old and replacing with new one. (When apply doesnt work)

![](Images/Pasted%20image%2020230212202542.png)



<br/>
<br/>

---

---
<br/>
<br/>


# To execute commands on a pod using exec

`kubectl -n elastic-stack exec -it app -- cat /log/app.log`

> -n is for namespace
> app is pod name
> commands are after --


<br/>
<br/>

---

---
<br/>
<br/>


# To get logs of a pod directly

`kubectl logs podname `

<br/>

# To get log of a particular container in a pod

`kubectl logs podname -c containerName`

<br/>
<br/>

---

---
<br/>
<br/>


## To get container info, can use crictl for CRI compatible container runtimes

`crictl ps -a`




# kubectl proxy (not kube-proxy)

![](Images/Pasted%20image%2020230226222229.png)

Can start kube proxy server which will by default use the credentials in .kubeconfig



<br/>
<br/>

---

---
<br/>
<br/>





# To get namespace scoped or cluster scoped api resources 

![](Images/Pasted%20image%2020230226233222.png)


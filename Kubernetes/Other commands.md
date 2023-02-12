To monitor pods status live 
`kubectl get pods --watch`

---

<br/>
<br/>

---

### To get yaml output for a command without executing 
`kubectl command --dry-run=client -o yaml`

`kubectl get pod podName -o yaml` to get yaml output (MUST USE GET NOT DESCRIBE)



---

<br/>
<br/>

---


## To show objects in all namespaces

`--namespace=all`



---

<br/>
<br/>

---



![](Images/Pasted%20image%2020230212035029.png)



---

<br/>
<br/>

---

# To force replace a resource , by deleting old and replacing with new one. (When apply doesnt work)

![](Images/Pasted%20image%2020230212202542.png)





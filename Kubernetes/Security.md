
# Basic Auth (deprecated in 1.19 version )

![](Images/Pasted%20image%2020230213212223.png)

or if cluster setup with kubeadm
![](Images/Pasted%20image%2020230213212305.png)



<br/>

### Then can Authenticate like this

![](Images/Pasted%20image%2020230213212440.png)




<br/>
<br/>

---

---
<br/>
<br/>






# Certificates

### Three types of certs, server , client and root certs(certs of CA)

![](Images/Pasted%20image%2020230225191545.png)




<br/>

### Certificates in kubernetes

![](Images/Pasted%20image%2020230225192518.png)


<br/>

## Creating certificates (openssl)

1.    Generate keys
	`openssl genrsa -out ca.key 2048`

2. Send a certificate signing request with the  Common Name 
	`openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr`

3. We can sign the certificates by ourselves that are called self signing certificates. We can do this for the certificates generated for the CA.
	`openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt`

For other certificates, we can use this self signed certificate to sign

<br/>

Example: We can use the ca.key and ca.crt generated above to sign the admin keys
![](Images/Pasted%20image%2020230225193607.png)


Must add the relevant group details in the cert sign request to identify these keys as the root/admin user
![](Images/Pasted%20image%2020230225193743.png)




<br/>

### Various certificates locations are defined in the kube-config service file or manifest file

![](Images/Pasted%20image%2020230225194304.png)





<br/>

## To view / get various details about an existing certificate

`openssl x509 -in /etc/kuberntes/pki/apiserver.crt -text -noout`

![](Images/Pasted%20image%2020230225194503.png)

> Use openssl help to get commands









<br/>
<br/>

---

---
<br/>
<br/>




# Certificates API



![](Images/Pasted%20image%2020230226005116.png)




- Create a private key
- create a cert signing request using the priv key
![](Images/Pasted%20image%2020230226005337.png)

<br/>

- Use the csr in kubernetes csr object. must base64 encode it 

![](Images/Pasted%20image%2020230226005427.png)

<br/>

- use kubectl apply to run the csr yaml file
- Then use the `kubectl certificate approve csr_name` to get it approved

![](Images/Pasted%20image%2020230226005522.png)


<br/>

- Can view the approved certificate and be used

![](Images/Pasted%20image%2020230226005614.png)





<br/>
<br/>

---

---
<br/>
<br/>





# KubeConfig

![](Images/Pasted%20image%2020230226014554.png)

**Contexts are used to define which user can access which cluster**

<br/>




## To switch to a context. Or to know current context

![](Images/Pasted%20image%2020230226015730.png)


<br/>
<br/>

---

---
<br/>
<br/>



# Authorization

- ABAC (Attribute based access control)
	Assigning rules manually to every user

- RBAC(Role based.. role with permissions are created and role is assigned to respective user)

- Webhook (3rd party authorization management.. example: open policy agent)

- AlwaysAllow, AlwaysDeny

> AlwaysAllow is set by default 

<br/>

##### Can set multiple modes. will check them in order

![](Images/Pasted%20image%2020230226030940.png)



<br/>
<br/>

---

---
<br/>
<br/>



# RBAC
Role Based Access Controls



## Role Object (Namespace scoped)

![](Images/Pasted%20image%2020230226220831.png)

- In the rules, we need not to define the apiGroups for Core APIs. For any other group, we need to mention the group name

<br/>

## Can also give access to specific resource using its resource name

![](Images/Pasted%20image%2020230226223507.png)







<br/>

## Must create a role binding object after creating role object

![](Images/Pasted%20image%2020230226222641.png)

> By default, configured for default namespace tho can set a custom namespace too in the metadata of binding file






<br/>


![](Images/Pasted%20image%2020230226222821.png)






<br/>
<br/>

---

---
<br/>
<br/>



# To check access

`kubectl auth can-i create deployments`

<br/>

**Can also specify a user to check the user's access**
`kubectl auth can-i cerate deployments --as dev-user `

`kubectl auth can-i delete pods --as dev-user --namespace testNamespace`




<br/>
<br/>

---

---
<br/>
<br/>



# Cluster Roles and Cluster role bindings

These are created for cluster scoped objects access.


![](Images/Pasted%20image%2020230226232934.png)


### We can use cluster role and bindings for namespace scoped resources too, but they will be applied namespace wide instead of a particular ns.




<br/>
<br/>

---

---
<br/>
<br/>





# Service accounts and tokens

![](Images/Pasted%20image%2020230227002601.png)

STEPS
- create sa
- create role and role binding with the sa in binding
- create token for sa `kubectl create token dashboard-sa`
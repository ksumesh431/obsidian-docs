
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
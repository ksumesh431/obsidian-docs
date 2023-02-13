



# For rollout status and history

![](Images/Pasted%20image%2020230212172944.png)



<br/>
<br/>

---

---
<br/>
<br/>



# Rolling vs Blue Green

Rolling updates and Blue-Green updates are two strategies for performing updates to your applications in Kubernetes. They are both designed to minimize downtime and ensure that your application is always available to your users.

A rolling update in Kubernetes involves updating your application incrementally, by updating one pod at a time. This allows you to ensure that your application continues to run during the update, with no downtime. Rolling updates are performed automatically by the Kubernetes controller, and can be configured to occur gradually, giving you control over the rate of update.

Blue-Green updates, on the other hand, involve creating a duplicate of your application, and then switching traffic between the two versions of your application. This allows you to update one version of your application while the other version continues to serve traffic, and then switch traffic to the updated version once it is ready. This approach provides a high level of reliability and availability during updates, as well as making it easy to roll back to a previous version of your application if needed.



<br/>
<br/>

---

---
<br/>
<br/>


# Rollback a deployment 

![](Images/Pasted%20image%2020230212173844.png)

<br/>
<br/>

---

---
<br/>
<br/>

# Rolling Update 


![](Images/Pasted%20image%2020230212175909.png)


<br/>
<br/>

---

---
<br/>
<br/>


# command and args

## In docker
> "ENTRYPOINT" is the default command that runs on container startup
> "CMD" is the default parameter that appends to entrypoint. It gets overwritten if we pass somethuing else while running the container.

<br/>

## In k8s
> ENTRYPOINT = "command"
> CMD = "args"
> 

``` 
apiVersion: v1

kind: Pod

metadata:

	  name: command-demo
	
	  labels:
	
		    purpose: demonstrate-command

spec:

	  containers:
	
		  - name: command-demo-container
		
		    image: debian
		
		    command: ["printenv"]
		
		    args: ["HOSTNAME", "KUBERNETES_PORT"]
		
	  restartPolicy: OnFailure


```

Can also use just command like this
![](Images/Pasted%20image%2020230212202117.png)


<br/>
<br/>

---

---
<br/>
<br/>

# Environment Variables

Can be set using basic env syntax, configmaps, and secrets

![](Images/Pasted%20image%2020230212204043.png)



<br/>
<br/>

---

---
<br/>
<br/>


# ConfigMaps

![](Images/Pasted%20image%2020230212204532.png)


### Declarative way

![](Images/Pasted%20image%2020230212204754.png)

<br/>

For attaching multiple configmaps to same pod,
![](Images/Pasted%20image%2020230212205155.png)

<br/>

Can also add a single key value pair from a config map
![](Images/Pasted%20image%2020230212205339.png)



<br/>
<br/>

---

---
<br/>
<br/>



# Secrets

Imperative
![](Images/Pasted%20image%2020230212210908.png)

<br/>

Declarative
![](Images/Pasted%20image%2020230212211119.png)

Must store the values in encoded form
![](Images/Pasted%20image%2020230212211149.png)

<br/>


## To view secrets

![](Images/Pasted%20image%2020230212211339.png)



## Adding secrets as env vars in pods

![](Images/Pasted%20image%2020230212211518.png)
Can pass secret as a volume to the pod. Just like configmaps
> If secrets are passed as a volume, it creates a file for each  attribute  and the secret's value is the content of the file


<br/>

Example: ![](Images/Pasted%20image%2020230212211547.png)





<br/>
<br/>

---

---
<br/>
<br/>





# Multi-container pod with shared volume

![](Images/Pasted%20image%2020230212232023.png)




<br/>
<br/>

---

---
<br/>
<br/>




# Init containers

In a multi container setup,if we want to make sure a particular container runs till completion, we can define it under init container.

The process in initContainer must run to completion before the other containers start.

If any of the initContainers fail to complete, Kubernetes restarts the Pod repeatedly until the Init Container succeeds.

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']

```


### Use Cases
Statrtup script, initialising databses etc.
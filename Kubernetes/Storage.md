# Volumes and volumeMounts

- The host path is the path on the Node . (must be lowercase name)

- volumeMount is the the path inside the container where the volume should be mounted. Mention the Volume name to mount it.



![](Images/Pasted%20image%2020230213025331.png)





<br/>

### Example: If we want to mount AWS EBS:

![](Images/Pasted%20image%2020230213025604.png)



<br/>
<br/>

---

---
<br/>
<br/>



# Persistent Volume 

In Kubernetes, a Persistent Volume (PV) is a piece of networked storage in the cluster that has been provisioned by an administrator. It provides a way for pods to access data stored on a disk or network storage outside of their local file system.

![](Images/Pasted%20image%2020230213030205.png)


Example:
``` 
apiVersion: v1
kind: PersistentVolume
metadata:
	name: pv-log\
spec:
	persistentVolumeReclaimPolicy: Retain 
	accessModes: 
		- ReadWriteMany 
	capacity:
		storage: 100Mi
	hostPath:
		path: /pv/log

```






<br/>
<br/>

---

---
<br/>
<br/>


# Persistent Volume Claim

A Persistent Volume Claim (PVC) is a request for storage made by a user.

> PVs and PVCs are cluster-wide resources, meaning they can be used by any pod in the cluster, not just a single namespace or deployment. This makes it possible to share storage resources across multiple applications, as well as providing a way to migrate data between different parts of your cluster.


![](Images/Pasted%20image%2020230213030723.png)





<br/>

### Once you create a PVC use it in a POD definition file by specifying the PVC Claim name under persistentVolumeClaim section in the volumes section like this:
``` 
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim

```

**The same is true for ReplicaSets or Deployments. Add this to the pod template section of a Deployment on ReplicaSet.


<u>A pvc wont terminate if a pod is using it.</u>



<br/>


## Important properties while creating pvc

![](Images/Pasted%20image%2020230213205149.png)


<br/>




## Access Modes 

![](Images/Pasted%20image%2020230213201018.png)


<br/>


## PV Reclaim policies

1.  Retain: When a PVC is released or deleted, the underlying storage resource is not deleted and remains available for use by other PVCs. This is useful when you want to manually manage the lifecycle of your storage resources and want to keep data available even after a PVC has been deleted.
    
2.  Recycle: When a PVC is released or deleted, the underlying storage resource is marked for reuse. The data stored on the volume is not deleted immediately, but rather is marked for deletion. The data is only deleted after a subsequent PVC is created and bound to the volume and the new PVC explicitly requests the deletion of the previous data.
    
3.  Delete: When a PVC is released or deleted, the underlying storage resource is deleted immediately and the data is lost. This reclaim policy is useful when you want to automatically delete storage resources that are no longer in use.








<br/>
<br/>

---

---
<br/>
<br/>



# Storage Class

We can define a storage class for various storage provisioners like gcp, aws ebs etc.
If we define a storage class, then it automatically creates a pv when required. that is when a pvc with the storage class requests for storage.

![](Images/Pasted%20image%2020230213202701.png)

<br/>

### the "no-provisioner" local provisioner

![](Images/Pasted%20image%2020230213204549.png)

![](Images/Pasted%20image%2020230213204945.png)








# Architecture

![](Images/Pasted%20image%2020230126032609.png)
<br/>
![](Images/Pasted%20image%2020230127013004.png)
<br/>
![](Images/Pasted%20image%2020230127013043.png)
<br/>



<br/>
<br/>

**When configuring k8s clusters manually, must start all components As a  Service instead of running directly**



<br/>
<br/>

![](Images/Pasted%20image%2020230130204046.png)


<br/>
<br/>



## There are always 2 ways of creating resources in kubernetes
- The imperative way i.e using the command line with arguments
- The declarative way i.e YAML files


<br/>
<br/>

---

---
<br/>
<br/>


# etcd
> Key value store that contains all the informations about various components like pods. nodes, secrets, etc

**Default port on which etcd listens is `2379`**


<br/>
<br/>

---

---
<br/>
<br/>

# kube-apiserver

**The only component that interacts directly with the etcd datastore.**

## Location of kube-apiserver options

If set up by kubeadm -> `/etc/kubernetes/manifests/kube-apiserver.yaml`

If set up manually as a service -> `/etc/systemd/system/kube-apiserver.service`



<br/>
<br/>

---

---
<br/>
<br/>

# kube-controller-manager

> Controls  various controllers like node-controller, replication-controller etc

 **Controllers keep a watch on various components and are responsible for remidiating situations**

## Location of kube-controller-manager options
In `/etc/kubernetes/manifests/` if set up by kubeadm
or the service directory `/etc/systemd/system/` in manual setup



<br/>
<br/>

---

---
<br/>
<br/>

# kube scheduler

>  Its only responsible for deciding that which Pod goes on which Node. Tho creating the pod on the nodes is done by the Kubelet only.

Its responsible for deciding by filtering the nodes based on various criteria, ranking them and deciding the suitable node for the pod.


<br/>
<br/>

---

---
<br/>
<br/>

# kubelet

NOTE: **kubelet doesnt automatically gets deployed with kubeadm , hence has to be manually deployed on the nodes**


<br/>
<br/>

---

---
<br/>
<br/>

# kube-proxy

While communicating with pods, the ip addresses cant be used as it changes with new pods, hence **services** are created for it.
But services are not running as container.
Services are virtual components in k8s memory.

kube-proxy is installed on each node and its job is to look for new services and to create ip tables for forwarding traffic to the services.

![](Images/Pasted%20image%2020230127012409.png)


> if we deploy using kubeadm, kube-proxy deploys as pods (Daemonset) on each node




<br/>
<br/>

---

---
<br/>
<br/>

# Pods (pod)

## Sample pod defination using yaml
![](Images/Pasted%20image%2020230127020754.png)


> can use any key value pair under the labels in metadata


<br/>

<br/>




<br/>

To run pod using command line
`kubectl run podName --image=nginx`

<br/>

` kubectl delete pods --all` to delete all running pods at once


<br/>
<br/>

---

---
<br/>
<br/>

# ReplicaSets (rs)


## ReplicationController (old)
![](Images/Pasted%20image%2020230201011101.png)


## ReplicaSet
![](Images/Pasted%20image%2020230201011142.png)


> The "selector" property differntiates it from replication controller
> the selector matches the existing pods too
> 
> **Must have same labels in selector and template**


## To scale the replicas, we can either edit the value to 6 and run `kubectl replace -f replica.yml`

## Or can use kubetcl scale

![](Images/Pasted%20image%2020230201011748.png)

*Using SCALE wont change the value of replicas in the yaml file*



<br/>
<br/>

---

---
<br/>
<br/>


# Deployments (deploy)

Deployments are used to specify many properties like deplyment stratagies of rolling or recreate deployment.
Only differnce from replicaset syntax is the "kind" field.


Can also use command line to create deployments directly without using yml
![](Images/Pasted%20image%2020230201161921.png)

![](Images/Pasted%20image%2020230201161934.png)





<br/>
<br/>

---

---
<br/>
<br/>


# Services (svc)

<br/>

## NodePort  

Exposes the service on each Node’s IP at a static port (the NodePort). A ClusterIP service, to which the NodePort service will route, is automatically created. You’ll be able to contact the NodePort service, from outside the cluster, by requesting `<NodeIP>:<NodePort>`

![](Images/Pasted%20image%2020230201162609.png)
![](Images/Pasted%20image%2020230201162714.png)
>  Selectors are used to match with the labels in the required pods 

<br/>

## ClusterIP

Exposes the service on a cluster-internal IP. Choosing this value makes the service only reachable from within the cluster. This is the default ServiceType






<br/>
<br/>

---

---
<br/>
<br/>


# Namespaces (ns)

To refer to services in same or differnt namespaces

![](Images/Pasted%20image%2020230202210326.png)

![](Images/Pasted%20image%2020230202210352.png)

<br/>

## Creating Namespaces 

![](Images/Pasted%20image%2020230202210453.png)

<br/>

## To change the default namespace
![](Images/Pasted%20image%2020230202210551.png)


<br/>

### For getting resources in all namespaces
`kubectl get deployments --all-namespaces`


<br/>

## Assigning resource quota for resources in a namespace
![](Images/Pasted%20image%2020230202211600.png)





<br/>
<br/>

---

---
<br/>
<br/>

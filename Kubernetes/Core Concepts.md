
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

---




# etcd
> Key value store that contains all the informations about various components like pods. nodes, secrets, etc

**Default port on which etcd listens is `2379` **


<br/>
<br/>


---


# kube-apiserver

**The only component that interacts directly with the etcd datastore.**

## Location of kube-apiserver options

If set up by kubeadm -> `/etc/kubernetes/manifests/kube-apiserver.yaml`

If set up manually as a service -> `/etc/systemd/system/kube-apiserver.service`



<br/>
<br/>

---

# kube-controller-manager
> Controls  various controllers like node-controller, replication-controller etc

 **Controllers keep a watch on various components and are responsible for remidiating situations **

## Location of kube-controller-manager options
In `/etc/kubernetes/manifests/` if set up by kubeadm
or the service directory `/etc/systemd/system/` in manual setup





<br/>
<br/>

---


# kube scheduler

>  Its only responsible for deciding that which Pod goes on which Node. Tho creating the pod on the nodes is done by the Kubelet only.

Its responsible for deciding by filtering the nodes based on various criteria, ranking them and deciding the suitable node for the pod.


<br/>
<br/>

---


# kubelet

NOTE: **kubelet doesnt automatically gets deployed with kubeadm , hence has to be manually deployed on the nodes**


<br/>
<br/>

---


# kube-proxy

While communicating with pods, the ip addresses cant be used as it changes with new pods, hence **services** are cerated for it.
But services are not running as container.
Services are virtual components in k8s memory.

kube-proxy is isntalled on each node and its job is to look for new services and to create ip tables for forwarding traffic to the services.

![](Images/Pasted%20image%2020230127012409.png)


> if we deploy using kubeadm, kube-proxy deploys as pods (Daemonset) on each node




<br/>
<br/>

---



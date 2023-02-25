
# Debugging scheduling failiures
https://www.datadoghq.com/blog/debug-kubernetes-pending-pods/#persistentvolume-related-scheduling-failures


<br/>
<br/>

---

---
<br/>
<br/>


# Manually scheduling an existing pod. Have to send a binding object as json in a post request

![](Images/Pasted%20image%2020230203010046.png)

<br/>
## Or can change the yaml file and use `kubectl replace --force -f nginx.yml` which will delete the existing pod and create it


<br/>
<br/>

---

---
<br/>
<br/>


# Labels and Selectors

![](Images/Pasted%20image%2020230204171726.png)

### selectors can also be used in the command line ![](Images/Pasted%20image%2020230204171821.png)



<br/>
<br/>

---

---
<br/>
<br/>



# Taints and Tolerations

## Taints (On NODES)
The tainted nodes will by default restrict all pods 
![](Images/Pasted%20image%2020230211231513.png)
> The **NoExecute** makes sure any existing "Pods which dont tolerate the taint" be removed from the node

**<u>**To UnTaint a node, use the same command as taint with a - in the end**</u>**
![](Images/Pasted%20image%2020230211233918.png)



<br/>

## Tolerations (On PODS)
The Pods which have the toleration to some specific taint will be able to get scedyuled on the respective node.

For the taint ![](Images/Pasted%20image%2020230211232237.png)

Set tolerations like this on the pod.yaml in Double Quotes ![](Images/Pasted%20image%2020230211232335.png)

<br/>

> **Note: Taints and Tolerations only make sure that the NODE doesnt have some pod scheduled. But doent ensure any specified pod to be sceduled on a particular node. For that purpous, NODE AFFINITY is used**



<br/>
<br/>

---

---
<br/>
<br/>


# Node Affinity



![](Images/Pasted%20image%2020230211235159.png)


## Types
![](Images/Pasted%20image%2020230211235140.png)


## There are various operators

Example: To schedule the pods on the nodes which have a specific key, we can use **EXISTS** operator ![](Images/Pasted%20image%2020230212020332.png)



<br/>
<br/>

---

---
<br/>
<br/>



# Resource Limits

![](Images/Pasted%20image%2020230212022302.png)





<br/>
<br/>

---

---
<br/>
<br/>





# DaemonSet
It ensures that one instance of the application is running on all pods
Use cases: Logging pod, kube-proxy etc.

Very similar syntax to replica sets , except the `kind: DaemonSet`


<br/>
<br/>

---

---
<br/>
<br/>



# Static Pods

Can be deployed by kubelet by putting the pod.yaml files in the manifest folder

Location of manifest folder can be determined by inspecting the following option of kubelet
![](Images/Pasted%20image%2020230212030136.png)

OR
![](Images/Pasted%20image%2020230212030159.png)

### Steps to delete existing static pod
- Determine the node on which the pod is running by the pod name suffix
- go to the pod and:
![](Images/Pasted%20image%2020230212031546.png)


<br/>

![](Images/Pasted%20image%2020230212030225.png)










Debugging scheduling failiures
https://www.datadoghq.com/blog/debug-kubernetes-pending-pods/#persistentvolume-related-scheduling-failures

---
<br/>
<br/>

# Manually scheduling an existing pod. Have to send a binding object as json in a post request

![](Images/Pasted%20image%2020230203010046.png)

<br/>
## Or can change the yaml file and use `kubectl replace --force -f nginx.yml` which will delete the existing pod and create it

---
<br/>
<br/>

# Labels and Selectors

![](Images/Pasted%20image%2020230204171726.png)

### selectors can also be used in the command line ![](Images/Pasted%20image%2020230204171821.png)


---
<br/>
<br/>


# Taints and Tolerations

## Taints (On NODES)
The tainted nodes will by default restrict all pods 
![](Images/Pasted%20image%2020230211231513.png)
> The **NoExecute** makes sure any existing "Pods which dont tolerate the taint" be removed

**<u>**To UnTaint a node, use the same command as taint with a - in the end**</u>**
![](Images/Pasted%20image%2020230211233918.png)



<br/>

## Tolerations (On PODS)
The Pods which have the toleration to some specific taint will be able to get scedyuled on the respective node.

For the taint ![](Images/Pasted%20image%2020230211232237.png)

Set tolerations like this on the pod.yaml in Double Quotes ![](Images/Pasted%20image%2020230211232335.png)

<br/>

> **Note: Taints and Tolerations only make sure that the NODE doesnt have some pod scheduled. But doent ensure any specified pod to be sceduled on a particular node. For that purpous, NODE AFFINITY is used**




---
<br/>
<br/>



# Node Affinity



![](Images/Pasted%20image%2020230211235159.png)


## Types
![](Images/Pasted%20image%2020230211235140.png)


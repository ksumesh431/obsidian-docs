

# For OS Upgrades on a particular node

- We can drain the node which makes the node unschedulable and also moves the pods to other available nodes
   `kubectl drain node-1`
   
- Or we can just use "cordon" to make node unschedulable
  `kubectl cordon node-1`

> After the node is ready, we can use `kubectl uncordon node-1` to make it available for scheduling again



<br/>
<br/>

---

---
<br/>
<br/>




# Allowed version upgrades to main control plane components

![](Images/Pasted%20image%2020230213002231.png)


![](Images/Pasted%20image%2020230401022932.png)



# Consists of atleast 2 API server nodes and three etcd nodes




<br/>
<br/>

---

---
<br/>
<br/>


 
# Create cluster (without nodegroup)

``` 
eksctl create cluster --name=kpeks \
--region=us-east-1 \
--zones=us-east-1a,us-east-1b \
--without-nodegroup

```



<br/>
<br/>

---

---
<br/>
<br/>



# Create & Associate IAM OIDC Provider for our EKS Cluster

It enables to usage of IAM for allowing various services to access the cluster

``` 
eksctl utils associate-iam-oidc-provider \
--region us-east-1 \
--cluster kpeks \
--approve

```



<br/>
<br/>

---

---
<br/>
<br/>


# Create Node Group with additional Add-Ons in Public Subnets

``` 

# Create Public Node Group   
eksctl create nodegroup --cluster=kpeks \
 --region=us-east-1 \
 --name=kpeks-ng-public1 \
 --node-type=t3.medium \
--nodes=2 \
--nodes-min=2 \
--nodes-max=4 \
 --node-volume-size=20 \
  --ssh-access \
 --ssh-public-key=eks \
--managed \
--asg-access \
--external-dns-access \
--full-ecr-access \
--appmesh-access \
--alb-ingress-access 





# To get Nodegroups in a cluster
eksctl get nodegroup --cluster=kpeks
```


# To get the current context of kubectl

`kubectl config view --minify`



<br/>
<br/>

---

---
<br/>
<br/>




# To delete cluster using eksctl

> Must revert any manual changes before doing this
> Deleting the cluster automatically detes underlying node groups

`eksctl delete cluster kpeks`

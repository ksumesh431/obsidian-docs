## helm install

- 1st we add the helm repo to our local environment
- then we can install the chart from that repo

![](Images/Pasted%20image%2020230829233520.png)


<br/>
<br/>
<br/>

<br/>
<br/>
<br/>


# List Helm Repositories
helm repo list

<br/>

---

---
<br/>

# Add Helm Repository
helm repo add <DESIRED-NAME> <HELM-REPO-URL>
`helm repo add mybitnami https://charts.bitnami.com/bitnami`

<br/>

---

---
<br/>

# List Helm Repositories
helm repo list

<br/>

---

---
<br/>

# Search Helm Repository
``` 
helm search repo <KEY-WORD>
helm search repo nginx
helm search repo apache
helm search repo wildfly
```
<br/>

---

---
<br/>


# Update Helm Repo to  Make sure we get the latest list of charts
`helm repo update ` 

# Install Helm Chart
``` 
helm install <RELEASE-NAME> <repo_name_in_your_local_desktop/chart_name>
helm install mynginx mybitnami/nginx
```

# To uninstall
`helm uninstall release-name`

<br/>

---

---
<br/>


# helm upgrade
to upgrade the helm release to new **version tag**
``` 
# Review the Docker Image Versions we are using
https://github.com/users/stacksimplify/packages/container/package/kubenginx
Image Tags: 1.0.0, 2.0.0, 3.0.0, 4.0.0


helm upgrade <RELEASE-NAME> <repo_name_in_your_local_desktop/chart_name> --set <OVERRIDE-VALUE-FROM-values.yaml>

```
`helm upgrade myapp1 stacksimplify/mychart1 /
--set "image.tag=2.0.0"`


<br/>

---

---
<br/>


# helm list (to get deployed images)
`helm list --deployed` to get current release
`helm release --superseded` to get one version old release
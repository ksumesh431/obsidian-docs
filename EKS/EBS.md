## EKS Storage
- EBS CSI Driver
- EFS CSI Driver
- FSx for Luster CSI



<br/>
<br/>
<br/>

---
---
---
<br/>
<br/>
<br/>


# EBS CSI Driver

> What is a container storage interface?

The Container Storage Interface is **a community-based project for developing a standardized API enabling communication between container orchestration (CO) platforms and storage plugins**. In theory, a standardized communication protocol allows storage providers to write plugins more easily, to just one specification.


## Step-01: Introduction

- Create IAM Policy for EBS
- Associate IAM Policy to Worker Node IAM Role
- Install EBS CSI Driver

## [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/04-EKS-Storage-with-EBS-ElasticBlockStore/04-01-Install-EBS-CSI-Driver#step-02--create-iam-policyy)Step-02: Create IAM policyy

- Go to Services -> IAM
- Create a Policy
    - Select JSON tab and copy paste the below JSON

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:CreateSnapshot",
        "ec2:CreateTags",
        "ec2:CreateVolume",
        "ec2:DeleteSnapshot",
        "ec2:DeleteTags",
        "ec2:DeleteVolume",
        "ec2:DescribeInstances",
        "ec2:DescribeSnapshots",
        "ec2:DescribeTags",
        "ec2:DescribeVolumes",
        "ec2:DetachVolume"
      ],
      "Resource": "*"
    }
  ]
}
```

- Review the same in **Visual Editor**
- Click on **Review Policy**
- **Name:** Amazon_EBS_CSI_Driver
- **Description:** Policy for EC2 Instances to access Elastic Block Store
- Click on **Create Policy**

## [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/04-EKS-Storage-with-EBS-ElasticBlockStore/04-01-Install-EBS-CSI-Driver#step-03-get-the-iam-role-worker-nodes-using-and-associate-this-policy-to-that-role)Step-03: Get the IAM role Worker Nodes using and Associate this policy to that role

```
# Get Worker node IAM Role ARN
kubectl -n kube-system describe configmap aws-auth

# from output check rolearn
rolearn: arn:aws:iam::180789647333:role/eksctl-eksdemo1-nodegroup-eksdemo-NodeInstanceRole-IJN07ZKXAWNN
```

- Go to Services -> IAM -> Roles
- Search for role with name **eksctl-eksdemo1-nodegroup** and open it
- Click on **Permissions** tab
- Click on **Attach Policies**
- Search for **Amazon_EBS_CSI_Driver** and click on **Attach Policy**

## [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/04-EKS-Storage-with-EBS-ElasticBlockStore/04-01-Install-EBS-CSI-Driver#step-04-deploy-amazon-ebs-csi-driver)Step-04: Deploy Amazon EBS CSI Driver

- Verify kubectl version, it should be 1.14 or later

```
kubectl version --client --short
```

- Deploy Amazon EBS CSI Driver

```
# Deploy EBS CSI Driver
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"

# Verify ebs-csi pods running
kubectl get pods -n kube-system
```



<br/>
<br/>
<br/>

---
---
---
<br/>
<br/>
<br/>



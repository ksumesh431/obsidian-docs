![](Images/Pasted%20image%2020230611031946.png)

<br/>
<br/>
<br/>

---
---
---
<br/>
<br/>
<br/>




# Least privilege policy for eksctl
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "cloudformation:*",
                "ec2:*",
                "autoscaling:Describe*",
                "autoscaling:UpdateAutoScalingGroup",
                "eks:*",
                "iam:*",
                "ssm:Describe*",
                "ssm:Get*",
                "ssm:List*"
            ],
            "Resource": "*"
        }
    ]
}
```


## Step-01: Create EKS Cluster using eksctl

- It will take 15 to 20 minutes to create the Cluster Control Plane

```
# Create Cluster
eksctl create cluster --name=eksdemo1 \
                      --region=us-east-1 \
                      --zones=us-east-1a,us-east-1b \
                      --without-nodegroup 

# Get List of clusters
eksctl get cluster 
```


## Step-02: Create & Associate IAM OIDC Provider for our EKS Cluster

- To enable and use AWS IAM roles for Kubernetes service accounts on our EKS cluster, we must create & associate OIDC identity provider.
- To do so using `eksctl` we can use the below command.
- Use latest eksctl version (as on today the latest version is `0.21.0`)

```
# Template
eksctl utils associate-iam-oidc-provider \
    --region region-code \
    --cluster <cluter-name> \
    --approve

# Replace with region & cluster name
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster eksdemo1 \
    --approve
```

## [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#step-03-create-ec2-keypair)Step-03: Create EC2 Keypair

- Create a new EC2 Keypair with name as `kube-demo`
- This keypair we will use it when creating the EKS NodeGroup.
- This will help us to login to the EKS Worker Nodes using Terminal.

## [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#step-04-create-node-group-with-additional-add-ons-in-public-subnets)Step-04: Create Node Group with additional Add-Ons in Public Subnets

- These add-ons will create the respective IAM policies for us automatically within our Node Group role.

```
# Create Public Node Group   
eksctl create nodegroup --cluster=eksdemo1 \
                       --region=us-east-1 \
                       --name=eksdemo1-ng-public1 \
                       --node-type=t3.medium \
                       --nodes=2 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=kube-demo \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access 
```


## Step-05: Verify Cluster & Nodes

### [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#verify-nodegroup-subnets-to-confirm-ec2-instances-are-in-public-subnet)Verify NodeGroup subnets to confirm EC2 Instances are in Public Subnet

- Verify the node group subnet to ensure it created in public subnets
    - Go to Services -> EKS -> eksdemo -> eksdemo1-ng1-public
    - Click on Associated subnet in **Details** tab
    - Click on **Route Table** Tab.
    - We should see that internet route via Internet Gateway (0.0.0.0/0 -> igw-xxxxxxxx)

### [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#verify-cluster-nodegroup-in-eks-management-console)Verify Cluster, NodeGroup in EKS Management Console

- Go to Services -> Elastic Kubernetes Service -> eksdemo1

### [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#list-worker-nodes)List Worker Nodes

```
# List EKS clusters
eksctl get cluster

# List NodeGroups in a cluster
eksctl get nodegroup --cluster=<clusterName>

# List Nodes in current kubernetes cluster
kubectl get nodes -o wide

# Our kubectl context should be automatically changed to new cluster
kubectl config view --minify
```

### [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#verify-worker-node-iam-role-and-list-of-policies)Verify Worker Node IAM Role and list of Policies

- Go to Services -> EC2 -> Worker Nodes
- Click on **IAM Role associated to EC2 Worker Nodes**

### [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#verify-security-group-associated-to-worker-nodes)Verify Security Group Associated to Worker Nodes

- Go to Services -> EC2 -> Worker Nodes
- Click on **Security Group** associated to EC2 Instance which contains `remote` in the name.

### [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#verify-cloudformation-stacks)Verify CloudFormation Stacks

- Verify Control Plane Stack & Events
- Verify NodeGroup Stack & Events

### [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#login-to-worker-node-using-keypai-kube-demo)Login to Worker Node using Keypai kube-demo

- Login to worker node

```
# For MAC or Linux or Windows10
ssh -i kube-demo.pem ec2-user@<Public-IP-of-Worker-Node>

# For Windows 7
Use putty
```

## [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#step-06-update-worker-nodes-security-group-to-allow-all-traffic)Step-06: Update Worker Nodes Security Group to allow all traffic

- We need to allow `All Traffic` on worker node security group

## [](https://github.com/stacksimplify/aws-eks-kubernetes-masterclass/tree/master/01-EKS-Create-Cluster-using-eksctl/01-02-Create-EKSCluster-and-NodeGroups#additional-references)Additional References

- [https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html](https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html)
- [https://docs.aws.amazon.com/eks/latest/userguide/create-service-account-iam-policy-and-role.html](https://docs.aws.amazon.com/eks/latest/userguide/create-service-account-iam-policy-and-role.html)




# To get config
`aws eks --region us-east-1 update-kubeconfig --name eksdemo1`




---

Delete Cluster  
- We can delete cluster using `eksctl delete cluster`
```
# Delete Cluster
eksctl delete cluster <clusterName>
eksctl delete cluster eksdemo1
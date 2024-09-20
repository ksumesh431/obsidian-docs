vpc

 3 private subnets
 all 3 subnets connected to a nat in public subnet
 bastion in public subnet



# NOTES
- nlb should be used as tcp 6443 utilising
- target grp health chk should be chnaged to tcp 


add tg (forwarding to tcp 6443)

create two master nodes 
master nodes sg config
### **Example Inbound Security Group Configuration:**

|**Type**|**Protocol**|**Port Range**|**Source**|**Description**|

######################################## add lb sg later ###################################### 

|Custom TCP Rule|TCP|6443|Load balancer security group|Allow API server access via LB|
|Custom TCP Rule|TCP|6443|Worker node security group|Allow worker nodes to communicate with API server|
|Custom TCP Rule|TCP|2379-2380|Control plane security group|Allow etcd communication between control planes|
|SSH|TCP|22|Your IP (or admin subnet)|Allow SSH access for cluster administration|

###### TF CODE FOR REFERENCE
``` tf
resource "aws_security_group" "kubeadm_demo_sg_control_plane" {

name = "kubeadm-control-plane security group"

ingress {

description = "API Server"

protocol = "tcp"

from_port = 6443

to_port = 6443

cidr_blocks = ["0.0.0.0/0"]

}

  

ingress {

description = "Kubelet API"

protocol = "tcp"

from_port = 2379

to_port = 2380

cidr_blocks = ["0.0.0.0/0"]

}

  

ingress {

description = "etcd server client API"

protocol = "tcp"

from_port = 10250

to_port = 10250

cidr_blocks = ["0.0.0.0/0"]

}

  

ingress {

description = "Kube Scheduler"

protocol = "tcp"

from_port = 10259

to_port = 10259

cidr_blocks = ["0.0.0.0/0"]

}

  

ingress {

description = "Kube Contoller Manager"

protocol = "tcp"

from_port = 10257

to_port = 10257

cidr_blocks = ["0.0.0.0/0"]

}

  

tags = {

Name = "Control Plane SG"

}

}

  
  

resource "aws_security_group" "kubeadm_demo_sg_worker_nodes" {

name = "kubeadm-worker-node security group"

  

ingress {

description = "kubelet API"

protocol = "tcp"

from_port = 10250

to_port = 10250

cidr_blocks = ["0.0.0.0/0"]

}

  

ingress {

description = "NodePort services"

protocol = "tcp"

from_port = 30000

to_port = 32767

cidr_blocks = ["0.0.0.0/0"]

}

  

tags = {

Name = "Worker Nodes SG"

}

  

}

```



add master nodes to target grp 

create lb for tg
lb port http port 80
lb sg port 80 open

create worker node 


setup everything in the master nodes
https://infotechys.com/install-a-kubernetes-cluster-on-rhel-9/
.

can also refer
https://medium.com/@murtazavasi.dev/setup-kubernetes-ha-cluster-in-aws-using-kubeadm-part-2-the-how-b7614166af71
 

sudo kubeadm init --control-plane-endpoint "master1.naijalabs.net:6443" --upload-certs --pod-network-cidr=10.244.0.0/16










-----------------------------------------




# Manual setup 2nd attempt



kubeadm config image pull (before init)

in tf, (have to wait for load balancer provisioning)
also need to wait for user data setup completely before executing playbook for kubeadm init


sudo kubeadm init --control-plane-endpoint "ha-kubeadm-nlb-caec3bdfd3ca573c.elb.us-east-1.amazonaws.com:6443" --upload-certs --pod-network-cidr=10.244.0.0/16










---------------------




final console setup (WORKING AND TESTED)

https://youtu.be/FHXucrzqcZM?si=T7uOrtsbUmcO4duZ




















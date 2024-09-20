
----
manual infra setup

vpc setup
![](Images/Pasted%20image%2020240907203017.png)

- vpc
- 2 az
- 2 private nd 2 punlic subnets
- 1 nat gw
- 2 private route tables, connecting both private subnets to nat gateway and vpc s3 endpoint
- 1 public rtb connecting 2 public subnets to igw
- dns hostnames and dns resolution enabled

Create a security group for master nd worker node in same vpc
allow all traffic from the vpc cidr


-- ec2 instances setup

	# Keys setup , can do in tf by generating key pair, and using pub key in user data of master and worker. also copy in bastion the private key with 400 perm
while creating master and worker nodes, generate a ssh key pair , and add the public key to ~/.ssh/authorised_keys of both nodes. so that can use the private key to ssh from bastion to master/worker node






run the command on each node

``` 
sudo apt update  
sudo apt install docker.io -y  
sudo systemctl start docker  
sudo systemctl enable docker
sudo usermod -aG docker ${USER}


sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl kubernetes-cni
sudo systemctl enable kubelet
sudo systemctl start kubelet

sudo ufw disable
sudo swapoff -a
```

### on master
```
sudo kubeadm init
```

Which gives the output:

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.0.138.49:6443 --token obbe9e.f22axwe4g0fuazv5 \
	--discovery-token-ca-cert-hash sha256:09d24b8b7ebe0feedbecc97a60a2291e369f8406015728d22c54d10cb9a76210


Get the join comamnd so that can execute it on worker

### on master, now run
mkdir -p $HOME/.kube  
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config  
sudo chown $(id -u):$(id -g) $HOME/.kube/config     
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/v0.22.2/Documentation/k8s-old-manifests/kube-flannel-rbac.yml



### on worker
sudo kubeadm join 10.0.138.49:6443 --token obbe9e.f22axwe4g0fuazv5 \
	--discovery-token-ca-cert-hash sha256:09d24b8b7ebe0feedbecc97a60a2291e369f8406015728d22c54d10cb9a76210



---

### Then with local exec,

private nodes setup. can use local exec:

use bastion host to go to master node, copy  /etc/kubernetes/admin.conf to bastion and then to local

use the shells script
``` 
#!/bin/bash

# Define variables
BASTION_USER="bastion_user"
BASTION_HOST="bastion_public_ip"
PRIVATE_USER="private_instance_user"
PRIVATE_HOST="private_instance_private_ip"
BASTION_KEY_PATH="/path/to/bastion.pem"
PRIVATE_KEY_PATH="/path/to/privateserver.pem"
LOCAL_DESTINATION_PATH="/path/to/local/destination/file"
TEMP_FILE_PATH="/tmp/remote_file"

# Copy file from private instance to bastion
scp -i "${PRIVATE_KEY_PATH}" "${PRIVATE_USER}@${PRIVATE_HOST}:/path/to/remote/file" "${TEMP_FILE_PATH}"

# Copy file from bastion to local machine
scp -i "${BASTION_KEY_PATH}" "${BASTION_USER}@${BASTION_HOST}:${TEMP_FILE_PATH}" "${LOCAL_DESTINATION_PATH}"

# Clean up temporary file on bastion server
ssh -i "${BASTION_KEY_PATH}" "${BASTION_USER}@${BASTION_HOST}" "rm ${TEMP_FILE_PATH}"

```
resource "null_resource" "copy_file" {
  provisioner "local-exec" {
    command = "bash copy_file.sh"
  }

  # Optionally, you can define triggers to run the provisioner only when needed
  triggers = {
    always_run = "${timestamp()}"
  }
}

use the local exec to execute the script on bastion




edit kubeconfig to have
server: https://master:6443

also edit local environment /etc/hosts to ahve
127.0.0.1 master

because master is a valid name as per api server certificates



create systemd service to do port forwarding of 6443 port from localhost to master node

``` 
[Unit]
Description=Start SSH tunnel to Kubernetes master node

[Service]
ExecStart=/usr/bin/ssh -i <path-to-your-ssh-key> -L 6443:<master-private-ip>:6443 ubuntu@<bastion-public-ip> -N
Restart=always
User=<your-username>

[Install]
WantedBy=multi-user.target
```


Add export kubeconfig to bashrc
`echo 'export KUBECONFIG=<path-to-your-kubeconfig-file>/admin.conf' >> ~/.bashrc`
source ~/.bashrc



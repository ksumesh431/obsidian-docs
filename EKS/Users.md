Use this video as reference to: 

create multiple aws iam users
Give different k8s permissions using rbac roles
Then add the iam users to aws_auth configmap

Will use the aws user creds to switch to the iam user and then get the kube context for that specific user 

In a jenkins pipeline, will use the iam user creds and kubecontext to run the deploy pipeline 
https://youtu.be/cB0fCMPIj1Y?si=19tW9-VCskTTyDSk
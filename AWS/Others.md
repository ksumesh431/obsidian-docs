


#  Session Manager Setup

To make an Ubuntu EC2 instance ready for Session Manager, you need to follow these steps:

1.  First, make sure that the EC2 instance is running and has internet connectivity.
    
2.  Attach an IAM role to the instance with the required permissions to access the Systems Manager service.
    
3.  Install the SSM agent on the instance. The SSM agent is a software agent that runs on your EC2 instances and enables you to execute commands and run scripts against your instances.
    
    To install the SSM agent on Ubuntu, you can run the following commands in the terminal:
    
    
    
    `sudo snap install amazon-ssm-agent --classic sudo systemctl enable snap.amazon-ssm-agent.amazon-ssm-agent.service sudo systemctl start snap.amazon-ssm-agent.amazon-ssm-agent.service`
    
4.  Configure the SSM agent to communicate with the Systems Manager service. You can do this by running the following command in the terminal:
    
    
    
    `sudo nano /etc/amazon/ssm/amazon-ssm-agent.json`
    
    This will open the amazon-ssm-agent.json file in the nano text editor. Find the "aws" section and replace the "region" value with the region where your Systems Manager service is located. Save and close the file.
    
5.  Verify that the SSM agent is communicating with the Systems Manager service. You can do this by running the following command in the terminal:
    
    
    
    `sudo systemctl status snap.amazon-ssm-agent.amazon-ssm-agent.service`
    
    If the SSM agent is running and communicating with the Systems Manager service, you should see a "Active (running)" status message.
    
6.  Finally, enable Session Manager access for your EC2 instance. You can do this by attaching an IAM policy to the instance that allows it to be managed by Session Manager. The policy should include permissions to call the "StartSession" API action.
    
    You can create a custom IAM policy and attach it to the instance or use one of the pre-built AWS-managed policies such as "AmazonSSMManagedInstanceCore".
    

Once these steps are completed, you can connect to the EC2 instance using the AWS Systems Manager Session Manager feature to start a session without the need for a public IP address or any inbound ports to be opened.
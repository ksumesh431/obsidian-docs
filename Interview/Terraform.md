1. **What is Terraform?**
   - Terraform is an open-source infrastructure as code (IaC) tool used for provisioning and managing infrastructure resources.

2. **How does Terraform maintain the state of the infrastructure?**
   - Terraform maintains the state of the infrastructure in a file (by default named terraform.tfstate). This file keeps track of the resources that have been created and their current state.

3. **What is the purpose of the terraform init command?**
   - `terraform init` initializes a working directory containing Terraform configuration files. It downloads the provider plugins and sets up the backend.

4. **Explain the difference between terraform apply and terraform plan.**
   - `terraform plan` shows the execution plan without making any changes, while `terraform apply` executes the plan and makes the necessary changes to the infrastructure.

5. **Explain the concept of Terraform workspaces.**
   - Terraform workspaces allow you to manage multiple environments or configurations from a single set of Terraform configuration files.

6. **What is the remote state in Terraform, and why is it important?**
   - Remote state in Terraform refers to storing the state file in a remote location, like an object storage service. It helps in collaboration and allows multiple users to work on the same infrastructure.

7. **Describe how to handle sensitive information (secrets) in Terraform.**
   - Sensitive information can be handled using Terraform variables, environment variables, or a secret management tool. Avoid hardcoding sensitive data directly in the configuration files.

8. **Explain the purpose of provisioners in Terraform.**
   - Provisioners in Terraform are used to execute scripts on local or remote machines as part of resource creation or destruction. They are typically used for tasks like configuration management or initializing resources.

9. **How can you implement conditional logic in Terraform?**
   - Conditional logic in Terraform can be implemented using the `count` and `for each` meta-arguments, as well as dynamic block constructs. These allow resources to be conditionally created based on certain conditions.

10. **Explain the concept of Infrastructure as Code (IaC).**
    - IaC is a practice that involves managing and provisioning infrastructure through machine-readable script files rather than physical hardware configuration or interactive configuration tools.


### System Overview

This architecture facilitates a bidirectional file transfer process between an internal Windows file share and external clients using SFTP. The system is built on AWS and uses AWS Transfer Family for the SFTP frontend, S3 as an intermediary storage layer, and Lambda functions for the core processing logic. Authentication is handled dynamically using a custom identity provider backed by API Gateway, a Lambda function, and DynamoDB.

---

### Core Components and their Roles

- **AWS Transfer Family:** Serves as the SFTP server endpoint (`s-prd-sftp-s3`) that external FTP clients connect to. It offloads the SFTP protocol handling and integrates with a custom identity provider for authentication and authorization.
    
- **S3 Bucket (`fl-prd-sftp-s3`):** Acts as the central, transient storage location for files in transit. It is the backend storage for the AWS Transfer Family server. User-specific folders are organized under the `successed/{customer_folder}/` prefix.
    
- **Windows File Share (`prod fileshare`):** The internal, on-premises, or EC2-based file server (`172.25.60.83`) which is the final destination for inbound files and the original source for outbound files.
    
- **DynamoDB Table (`SharedSvc_AWSFTP_Prod_DDB`):** Stores user account information required for authentication, such as username, password (encrypted), allowed IP ranges, public SSH keys, and the specific IAM role and home directory to assign upon successful login.
    
- **AWS Secrets Manager:** Securely stores the credentials (`FL-PRD-SFTP-SE-ACCOUNT`) needed by the Lambda functions to access the internal Windows file share.
    
- **Lambda Functions:**
    
    - **Identity Lambda:** Acts as a custom identity provider for AWS Transfer Family. When a user tries to log in, Transfer Family invokes this Lambda via API Gateway. The Lambda validates the user's credentials and IP address against the data in DynamoDB, decrypting secrets with KMS, and returns the appropriate IAM role and S3 home directory settings to grant access.
        
    - **Outbound Lambda:** This function is triggered on a 15-minute schedule by Amazon EventBridge. It scans the `outbound` folder on the Windows file share, uploads any found files to the corresponding customer's `outbound` folder in the S3 bucket, and then deletes the files from the source file share. This makes the files available for the external client to download.
        
    - **Inbound Lambda:** This function is triggered by S3 object creation events via an SNS topic. When an external client uploads a file to their `inbound` folder in S3, this Lambda is invoked. It moves the file from S3 to the corresponding customer's `inbound` folder on the Windows file share and then deletes the file from S3.
        

### Workflows

#### **Inbound Flow (Client → File Share)**

1. An external client connects to the AWS Transfer Family SFTP endpoint.
    
2. AWS Transfer Family calls the **Identity Lambda** (via API Gateway) to authenticate the user. The Lambda checks their credentials and IP address against DynamoDB.
    
3. Upon successful authentication, the user is granted access to their designated home directory in the S3 bucket (e.g., `s3://fl-prd-sftp-s3/successed/customer_A/`).
    
4. The client uploads a file to their `inbound` folder. The file lands in S3 (e.g., `.../inbound/file.txt`).
    
5. The S3 `PutObject` event triggers an SNS notification, which in turn invokes the **Inbound Lambda**.
    
6. The Inbound Lambda reads the file from S3, copies it to the internal Windows file share at the correct path (e.g., `\\172.25.60.83\sftp\inbound\customer_A\file.txt`), and deletes the file from S3.
    

#### **Outbound Flow (File Share → Client)**

1. An internal process places a file in the Windows file share's outbound directory (e.g., `\\172.25.60.83\sftp\outbound\customer_A\report.csv`).
    
2. Every 15 minutes, an Amazon EventBridge schedule triggers the **Outbound Lambda**.
    
3. The Outbound Lambda connects to the file share, finds the new file, and uploads it to the customer's `outbound` folder in S3 (e.g., `s3://fl-prd-sftp-s3/successed/customer_A/outbound/report.csv`).
    
4. After a successful upload, the Lambda deletes the file from the Windows file share.
    
5. The external client can now log in via SFTP and download the `report.csv` file from their `outbound` folder.


> 
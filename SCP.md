
<br/>
<br/>



## S3 bucket SCP 


Here is an example SCP statement that should deny the creation of S3 buckets with public access

``` 
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyPublicS3BucketCreation",
            "Effect": "Deny",
            "Action": "s3:CreateBucket",
            "Resource": "arn:aws:s3:::*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "public-read-write"
                }
            }
        }
    ]
}


```


<br/>
<br/>


SCP (Service Control Policies) do not support the `Allow` statement or the `Principal` element. SCPs are used to restrict permissions for AWS accounts and cannot grant permissions beyond what is allowed by an account's IAM policies.

To allow specific IAM roles to create S3 buckets with public access, we can create an IAM policy that grants the `s3:CreateBucket` permission with the condition that the bucket's Access Control List (ACL) is set to `public-read-write`, and attach that policy to the appropriate IAM roles.

Here's an example IAM policy that allows a specific role to create S3 buckets with public access:
``` 
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "s3:CreateBucket",
            "Resource": "arn:aws:s3:::*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": [
                        "public-read-write",
                        "public-read"
                    ]
                }
            }
        }
    ]
}


```

In this example policy, the `Allow` statement grants the `s3:CreateBucket` permission with the condition that the bucket's ACL is set to `public-read` or `public-read-write`. 

We can attach this to the required IAM roles to give them exception of creating S3 buckets with public access.


<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

---

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>



## SCP to enforce tagging

``` 
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "EnforceTaggingPolicy",
            "Effect": "Deny",
            "Action": [
                "*"
            ],
            "Resource": [
                "*"
            ],
            "Condition": {
                "ForAllValues:StringNotEquals": {
                    "aws:TagKeys": [
                        "CreatorUser",
                        "CreationDate",
                        "CreationPurpose"
                    ]
                }
            }
        }
    ]
}


```

This policy denies all actions like creation on all resources unless they have the three required tags: `CreatorUser`, `CreationDate`, and `CreationPurpose`.


<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

---

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>



## SCP that would limit EC2 instances to sizes from micro to large and limit RDS instances from small to medium:

``` 
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LimitEC2InstanceSizes",
            "Effect": "Deny",
            "Action": "ec2:RunInstances",
            "Resource": "*",
            "Condition": {
                "ForAnyValue:StringNotEquals": {
                    "ec2:InstanceType": [
                        "t2.micro",
                        "t2.small",
                        "t2.medium",
                        "t2.large"
                    ]
                }
            }
        },
        {
            "Sid": "LimitRDSInstanceSizes",
            "Effect": "Deny",
            "Action": [
                "rds:CreateDBInstance",
                "rds:RestoreDBInstanceFromDBSnapshot",
                "rds:RestoreDBInstanceToPointInTime"
            ],
            "Resource": "*",
            "Condition": {
                "ForAnyValue:StringNotEquals": {
                    "rds:DatabaseClass": [
                        "db.t2.small",
                        "db.t2.medium"
                    ]
                }
            }
        }
    ]
}



```

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

---

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>


## SCP that would restrict any resource creation to the region us-west-2

``` 
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyResourcesCreationInRegions",
            "Effect": "Deny",
            "Action": "*",
            "Resource": "*",
            "Condition": {
                "StringNotEquals": {
                    "aws:RequestedRegion": "us-west-2"
                }
            }
        }
    ]
}

```
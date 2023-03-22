
# To store remote state in a different account

> Running terraform in account A and storing remote state file in account B's s3 bucket



---

<br/>
<br/>


##  1. Create a s3 bucket in account B


<br/>
<br/>


## 2. Create a IAM role in "account B" 
#assumed_role 

### Add account A in the trust relationships of this role

Select: "another AWS acocunt" while setting up the role and add the Account A id.
Or can create a normal role and add the Account A id in trust relationships later.
``` 
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::<Account-id>:root"
            },
            "Action": "sts:AssumeRole",
            "Condition": {}
        }
    ]
}

```


<br/>
<br/>
<br/>
<br/>

## 3. Give the required permissions to the role through policy

``` 
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:ListBucket",
                "s3:DeleteObject",
                "s3:GetBucketLocation"
            ],
            "Resource": [
                "arn:aws:s3:::tfremotestatemumbairegion",
                "arn:aws:s3:::tfremotestatemumbairegion/*"
            ]
        }
    ]
}

```


<br/>
<br/>
<br/>
<br/>


## 4. In "Account A" , create a role that allows to assume the role created in account B.

``` 
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Resource": "arn:aws:iam::<account-B-id>:role/tf-s3-access-role",
            "Action": "sts:AssumeRole"
        }
    ]
}
```

**Also add account B in trust relationship**



<br/>
<br/>
<br/>
<br/>



## 5. In terraform 

**Add the bucket name and role created in account B**

``` 

terraform {

  required_version = "~> 1.1"

  required_providers {

    aws = {

      source  = "hashicorp/aws"

      version = "~> 3.0"

    }

  }

  backend "s3" {

  bucket = "tfremotestatemumbairegion"

  key    = "terraform.tfstate"

  region = "ap-south-1"

  role_arn = "arn:aws:iam::<account_B_ID>:role/tf-s3-access-role"

  }

}

```


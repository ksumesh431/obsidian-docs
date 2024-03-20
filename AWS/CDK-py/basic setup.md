install node js LTS using nvm

npm install -g aws-cdk

`cdk --version` to chk isntallation

` cdk init sample-app --language=python`
or `cdk init app --language=python`

app is created with the name of the directory

Activate venv

`pip install -r requirements.txt`

Do this instead for slow downlaods on wsl `DISPLAY= pip install <packagename>`


---

## Structure of project

app.py is the entrypoint of the project

project_name/project_name.py is the file where we define the infrastructure code



---

Setup required permissions for cdk bootstrap then Do `cdk bootstrap` to initialize the basic project resources

``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "cloudformation:CreateChangeSet",
                "cloudformation:DeleteStack",
                "cloudformation:DescribeChangeSet",
                "cloudformation:DescribeStackEvents",
                "cloudformation:DescribeStacks",
                "cloudformation:ExecuteChangeSet",
                "cloudformation:GetTemplate",
                "cloudformation:DeleteChangeSet"
            ],
            "Resource": "arn:aws:cloudformation:*:*:stack/CDKToolkit/*",
            "Effect": "Allow",
            "Sid": "CloudFormationPermissions"
        },
        {
            "Action": [
                "iam:CreateRole",
                "iam:DeleteRole",
                "iam:GetRole",
                "iam:AttachRolePolicy",
                "iam:DetachRolePolicy",
                "iam:DeleteRolePolicy",
                "iam:PutRolePolicy"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:iam::*:policy/*",
                "arn:aws:iam::*:role/cdk-*"
            ]
        },
        {
            "Action": [
                "s3:CreateBucket",
                "s3:DeleteBucket",
                "s3:PutBucketPolicy",
                "s3:DeleteBucketPolicy",
                "s3:PutBucketPublicAccessBlock",
                "s3:PutBucketVersioning",
                "s3:PutEncryptionConfiguration"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::cdk-*"
            ]
        },
        {
            "Action": [
                "ssm:DeleteParameter",
                "ssm:GetParameter",
                "ssm:GetParameters",
                "ssm:PutParameter"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:ssm:*:*:parameter/cdk-bootstrap/*"
            ]
        },
        {
            "Action": [
                "ecr:CreateRepository",
                "ecr:DeleteRepository",
                "ecr:DescribeRepositories",
                "ecr:SetRepositoryPolicy"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:ecr:*:*:repository/cdk-*"
            ]
        }
    ]
}
```


---

`cdk ls` to get the rsources in our cdk and to check for errors
`cdk synth` to get cfn template

cdk deploy -vv to get verbose level logging
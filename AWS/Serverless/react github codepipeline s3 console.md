
# create s3 bucket with block public access disabled
add the following policy
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::ci-cd-demo-console/*"
        }
    ]
}

```

Enable static hosting option for the bucket



---

# create pipeline
![](Images/Pasted%20image%2020240224203846.png)

had to set up github conenction in the console manually
code pipeline execution role  , codebuild execution role was created automatically
Trusting Account (Sumesh)

Trusted Account (Reena)






STEPS TO BE DONE ON "TRUSTING ACCOUNT"

1. We get the account id of both accounts

Sumesh = 507074252730
Reena = 078352025563


2. Go to IAM

3. Create a new role (Click on create role)

4. Click on AWS account. ANd then "ANOTHER AWS ACCOUNT".

5. Enter the trusted account id here.

6. On next screen. Select the policy which you want to allow. (example: amazonec2 or amazons3)

7. Give a role name and STORE THE NAME. (assumedRoleForReena)

8. Click on create role.

9. Create a new group.

10. Create a new user. (Select password access)

11. On next screen, select the group we created previously.

12. Go to policies. and create a new policy.

13. Click on "Choose a service". Search for sts. and click on sts.

14. Under access level, click on arrow of "write" and select "assumeRole"

15. Click on resources.

16. CLick on "Add ARN"

17. On a new tab, open aws console, IAM, go to the created role. Copy the role "ARN" (arn:aws:iam::507074252730:role/reena-assumedRole)

18. Go to previous tab. and paste the ARN there.

19. Click on add.

20. Click on next. Put a Policy Name and create the policy.

21. Go to user groups. CLick on the assumed group. 

22. Go to permissions -> Add permssions -> Attach policy

23. Select the policy we just created above and click on add policy.







STEPS TO BE DONE IN "TRUSTED ACCOUNT"

1. Get the "TRUSTING ACCOUNT NUMBER" that is 507074252730

2. Get the Assumed Role Name  (reena-assumedRole)

3. Click on account name

4. Click on switch role

5. Enter the account number and role name



---

# Assumed role setup for one back-end account running boto3 queries on multiple customer accounts

To set up cross-account access so that your backend EC2 instance in the backend AWS account can assume roles in your customers' accounts, you'll need to configure both the backend IAM role (`backend_role`) and the IAM roles in each customer account (`role1`, `role2`, `role3`). Here are the detailed steps for both sides:

---

### Steps to be Followed in the "Backend Account"

#### 1. Verify or Create the Backend IAM Role (`backend_role`)
Your EC2 instance already has the IAM role `backend_role` attached to it, which allows it to use AWS CLI commands without needing explicit access keys. You will need to add policies to this role to allow it to assume roles in your customers' accounts.

- **Policy**: Add an IAM policy to `backend_role` that grants `sts:AssumeRole` permissions on each customer account's IAM role ARN (like `role1`, `role2`, `role3`).

Here’s an example of the policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": [
        "arn:aws:iam::<CustomerAccount1ID>:role/role1",
        "arn:aws:iam::<CustomerAccount2ID>:role/role2",
        "arn:aws:iam::<CustomerAccount3ID>:role/role3"
      ]
    }
  ]
}
```

Replace `<CustomerAccount1ID>`, `<CustomerAccount2ID>`, and `<CustomerAccount3ID>` with the actual AWS account IDs for each customer.

**Steps**:
1. Go to the IAM console in the backend account.
2. Locate and select `backend_role`.
3. Attach the above policy to `backend_role`.

### Steps to be Performed in Each Customer Account

For each customer account (e.g., `account1`, `account2`, `account3`), you'll need to create an IAM role (e.g., `role1`, `role2`, `role3`) with a trust policy that allows the `backend_role` from the backend account to assume it. You’ll also need to attach any necessary policies to each of these roles to grant permissions to resources within each account.

#### 1. Create IAM Role for the Backend Account to Assume
For each customer account, create a role (e.g., `role1`, `role2`, `role3`) with the following setup:

- **Trusted Entities (Trust Policy)**: This policy should allow the backend account to assume the role.

Here’s an example trust policy for `role1` (assuming `backend_account_id` is the account ID of the backend account):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<backend_account_id>:role/backend_role"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

Replace `<backend_account_id>` with the account ID of your backend AWS account.

**Steps**:
1. Go to the IAM console in the customer account.
2. Create a new role (e.g., `role1` for `account1`, `role2` for `account2`, and `role3` for `account3`).
3. Choose "Another AWS account" as the trusted entity.
4. Enter the ARN for the backend IAM role (`backend_role`), and add the trust policy above.

#### 2. Attach Permissions to Customer Account Roles
Attach any necessary permissions to `role1`, `role2`, and `role3` that you want your backend EC2 instance to have within each customer account. This could include permissions like `s3:ListBucket` or `ec2:DescribeInstances`, depending on the required access.

For example, to allow S3 read access, attach a policy like this to `role1` in `account1`:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3:::example-bucket",
        "arn:aws:s3:::example-bucket/*"
      ]
    }
  ]
}
```

**Steps**:
1. Still in the IAM console of each customer account, go to the role (e.g., `role1`).
2. Attach the necessary policy granting access to resources in that customer account.

---

### Summary of the Workflow

1. **Backend Account**:
   - Ensure `backend_role` has `sts:AssumeRole` permissions for each customer’s IAM role.
   
2. **Customer Accounts**:
   - Create an IAM role (e.g., `role1`) with a trust policy that allows the backend account’s IAM role (`backend_role`) to assume it.
   - Attach any necessary policies to `role1`, `role2`, `role3` to provide access to specific resources within each customer account.

With this setup complete, the `backend_role` on your EC2 instance should be able to assume `role1`, `role2`, or `role3` in each customer account using the code you provided.


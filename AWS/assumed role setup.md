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




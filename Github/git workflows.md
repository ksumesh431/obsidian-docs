
**Pulling latest changes from the remote MAIN branch**

step 1. git add . (if any changes on local branch )

step2. git commit  (if any changes on local branch )

step3. go to MAIN branch in local

step4. git fetch origin

step5. git pull origin MAIN

step6. go to your branch

step7. git rebase MAIN




### Bitbucket pull request merge conflicts

PR is to pull code from FEATURE branch to PROD branch.
In case of conflicts,

clone the feature branch to local
do a git pull origin PROD --no-rebase
solve the merge conflicts
add, commit and push
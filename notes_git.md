## how to enable multiple Github accounts on one device
`cd` to the directory that holds files to upload from a Github account different than the default global Github account.

Check the configurations of `user.name`, `user.email` first:
`
$ git config --list
`

Add the local account specified to this directory, can be the noreply one:
`
$ git config --local user.email "USERNAME@users.noreply.github.com"
`

Modify the url (optional):
`
$ git config remote.origin.url "https://github.com/USERNAME/REPO.git"
`

Check the configs again, which should has another line of the newly added account name. 
`
$ git config --list
`

Check out a new branch before `commit` and `push` (optional):
`
git checkout -b NEWBRANCH
`

Add new changes to the commit:
`
git add .
`

Check git status for staging files and untrackted files if there are any (optional):
`
$ git status
`

Commit and push:  
`
$ git commit -am "YOUR MESSAGE"
$ git push origin NEWBRANCH
`

Open the github webpage and check. 

Add the repo to local desktop, "Github Desktop" -> "File" -> "Add Local Repository". Choose the directory holding your files. Now make some changes to your files to test, and you should find the commits on Github page should be pushed from the local account specified previously. 


## how to revert a pushed commit
Formula:    
`
$ git reset --hard HEAD~n
$ git push -f origin VERSION_CODE:BRANCH_NAME
`

Example:     
Let's revert to one previous commit.
`
$ git reset --hard HEAD~1
`
which returns, "HEAD is now at 6a107b4 Rendered results of R Markdown Example". Now plug the "6a107b4" into the "VERSION_CODE":
`
$ git push -f origin 6a107b4:test_branch
`
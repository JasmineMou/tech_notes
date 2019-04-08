## how to use multiple Github accounts on Github Desktop 
- `cd` to the directory that holds files to upload from a Github account different than the default global Github account.
- Check the configurations of `user.name`, `user.email` first:    
`
$ git config --list
`
- Add the local account specified to this directory, can be the noreply one:        
`
$ git config --local user.email "USERNAME@users.noreply.github.com"
`
- Either modify the url or do the `remote add` if the url hasn't been configured once before:    
`
$ git config remote.origin.url "https://github.com/USERNAME/REPO.git"
`    
`
$ git remote add origin git@github.com:USERNAME/REPO.git
`
- Check the configs again, which should has another line of the newly added account name.    
`
$ git config --list
`
- Check out a new branch before `commit` and `push` (optional):    
`
$ git checkout -b NEWBRANCH
`
- Add new changes to the commit:    
`
$ git add .
`
- Check git status for staging files and untrackted files if there are any (optional):    
`
$ git status
`
- Commit and push:      
`$ git commit -am "YOUR MESSAGE"`         
`$ git push origin NEWBRANCH`       
- Open the github webpage and check. 
- Add the repo to local desktop. From the preferences do: "Github Desktop" -> "File" -> "Add Local Repository". Choose the directory holding your files. Now make some changes to your files to test, and you should find the commits on Github page should be pushed from the local account specified previously. 

#### ref
- https://stackoverflow.com/a/16682441/2687773
- https://alvinalexander.com/git/git-show-change-username-email-address
- add & commit: http://rogerdudler.github.io/git-guide/

## how to revert a pushed commit
Formula I:    
`$ git reset --hard HEAD~n`     
`$ git push -f origin OLD_VERSION_CODE:BRANCH_NAME`

Example:     
Let's revert to one previous commit.    
`
$ git reset --hard HEAD~1
`    
which returns, "HEAD is now at 6a107b4 Rendered results of R Markdown Example". 

Now plug the "6a107b4" into the "VERSION_CODE":     
`
$ git push -f origin 6a107b4:test_branch
` 

#### ref
https://stackoverflow.com/a/1270608/2687773
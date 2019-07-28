
## git branch sort by date
```
git branch --sort=-committerdate  # DESC
git branch --sort=committerdate  # ASC
```

## ignore ssl verify 
error
fatal: unable to access '<GIT_URL>': SSL certificate problem: unable to get local issuer certificate

Workaround
Disable Git SSL verification in the server hosting FishEye/Crucible with the following commands:
```
git config --global http.sslVerify false
```

## remove files/dir from staging
```
git reset HEAD -- file/dir
```
for removing all files from the git index.
```
git reset HEAD 
```

## rename local and remote branch 
https://multiplestates.wordpress.com/2015/02/05/rename-a-local-and-remote-branch-in-git/

## undo last commit before push
```
git reset --soft d93bd7879ca0f8e8bc0866660022b43098b63390
```
The last part is the commit log ID which you want to go back to.

## undo all local changes
```
git reset --hard
```

## undo add
```
git reset filename
```
will reove the file from index

## git log latest n commit
```
git log -10
```

## git stash 
```
git stash save "message"
git stash pop [–index] [stash_id]
or 
git stash apply [–index] [stash_id]
```
other stash commands
https://blog.csdn.net/daguanjia11/article/details/73810577

## tag
create tag
```
git tag <tag_name>
```
push tag
```
git push origin <tag_name>
or 
git push origin --tags
```
delete tag
```
git tag -d  <tag_name>
```
delete remote tag
```
git push origin --delete <tag_name>
```
https://git-scm.com/book/en/v2/Git-Basics-Tagging  
https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-tag  

## git add all modified 
the command to do git add all modified files, excluding the new files
```
git add -u
```

## rename a file
```
git mv old_filename new_filename
```


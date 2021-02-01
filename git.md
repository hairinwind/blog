
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

## git push a branch not exist on remote
to work on a branch not exist on remote  
create a branch on local 
```
git checkout -b local_branch_name
```
once the changes are checked in local branch
```
git push origin local_branch_name:remote_branch_name
```

## revert/undo last commit before push
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
or 
```
git rm --cached [file]
```

## git log latest n commit
```
git log -10
```
or by author
```
git log -10 --author=Jon
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

## stash selected files 
```
git stash push -u -k -m <stash_message> <file_list>
```
-u|--include-untracked : include untracked files
-k|--keep-index: the file in index is still in index after it is added to stash
--no-keep-index: the file in index is removed from index after it is added to stash

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

## to elete multiple branches
in Git bash
```
git branch | grep "<pattern>" | xargs git br
```
https://medium.com/@rajsek/deleting-multiple-branches-in-git-e07be9f5073c

## view local commit history
The changes was committed to local but not pushed yet. To view the local commit history
```
git log --branches --not --remotes -n
```
or 
```
git cherry -v
```

## git get current branch name
```
git symbolic-ref --short HEAD
```
to only assign the version to a linux env variable 
```
version=$(git symbolic-ref --short HEAD | cut -d '/' -f2)
```

## git get current tag name
```
version=$(git describe --exact-match --tags)
```

## git reset credential
Somehow I found git remembered my credential and did not ask me to input the credential anymore. After 3 months, the company policy enforced to change the password. The remembered credential did not work any more. Git did not popup for re-inputting password. It just failed and was showing authorization failed.  
Here is the command to list all git attributes 
```
git config -l
```
if you see "credential.helper=osxkeychain", it means git remembers your credential.  
To reset that 
```
git config --global credential.helper osxkeychain
```
In next git command like pull or push, git prompts username and password.

In windows, it is using "Credential Manager"
https://stackoverflow.com/questions/15381198/remove-credentials-from-git

## error when doing git pull
The error is like "Cannot lock ref 'refs/remotes/origin/master...' "
```
git remote prune origin
```
This command remove the local branch which has already been removed on remote

## git mode diff
After copy the workspace from the old machine, the file permission was changed from 644 to 755

Run git status, all files were marked changed. 
Run git diff one_file, 
```
old mode 100644
new mode 100755
```
Actually, most files do not need 755, 644 is enough
To change them back, just run 
```
find . -type f -exec chmod 644 {} \; 
```

## git checkout file from a specific commit
```
git checkout f08a63ff4fa7b8479f8c698e5998ee1afcac3a4e Gemfile
```

## git compare all changes from two commits
```
git diff a0921c77d107df99bd89877c0d708fb5ec27074e 29ae7af8bc04c3749806575e7f3486895a211b52
```
if only needs the file name changed between two commits
```
git diff a0921c77d107df99bd89877c0d708fb5ec27074e 29ae7af8bc04c3749806575e7f3486895a211b52 --name-only
```
if need see diff for one specific file between two commits
```
git diff a0921c77d107df99bd89877c0d708fb5ec27074e 29ae7af8bc04c3749806575e7f3486895a211b52 <file_name>
```

## git sparse-checkout subdirectories from repo
todo
https://stackoverflow.com/questions/600079/how-do-i-clone-a-subdirectory-only-of-a-git-repository

https://github.blog/2020-01-17-bring-your-monorepo-down-to-size-with-sparse-checkout/ 

the generated sparse-checkout is here .git/info/sparse-checkout 



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

## rename local and remote branch 
https://multiplestates.wordpress.com/2015/02/05/rename-a-local-and-remote-branch-in-git/

## undo last commit before push
```
git reset --soft d93bd7879ca0f8e8bc0866660022b43098b63390
```
The last part is the commit log ID which you want to go back to.

## git log latest n commit
```
git log -10
```



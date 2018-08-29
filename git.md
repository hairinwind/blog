
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
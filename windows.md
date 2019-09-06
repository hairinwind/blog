# windows

## merge multiple txt files into one
```
copy source_folder\*.* dest_folder\1.txt
```
All the files would be copied into one file

## which port is listening
```
netstat -noa |findstr "8080"
```

## WSL (windows subsystem linux)
run ubuntu as root 
```
C:\> ubuntu config --default-user root
```
https://docs.microsoft.com/en-us/windows/wsl/user-support


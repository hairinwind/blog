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
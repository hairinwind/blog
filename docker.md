# docker 

## install mysql
https://dev.mysql.com/doc/refman/5.7/en/docker-mysql-getting-started.html

## run the script inside the container
```
docker exec -it mysql1 mysql -uroot -p
```
Or 
```
docker exec -it mysql1 bash 
```

## remove images
```
docker rmi imageNameOrId
```
 
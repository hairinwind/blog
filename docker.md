# docker 

## list all containers in the repository
```
docker container ls -a
```

## list all running containers
```
docker ps
```

## start one container
```
docker start <container_name>
```
stop all running containers
```
sudo docker stop $(sudo docker ps -aq)
```

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

## docker-compose up and down
to start up, go to the directory having the file docker-compose.yml, run 
```
sudo docker-compose up -d
```
to turn it off, in the same folder, run 
```
sudo docker-compose down
```

## get into the container
```
docker exec -it name-of-container bash
```
to quit the container, press "ctrl+d" or type "exit"

## view docker log
```
docker logs <container_name>
```

 ## TBD 
 docker run \
  --net=host \
  --rm \
  confluentinc/cp-kafka:3.1.2 \
  kafka-topics --create --topic bar --partitions 3 --replication-factor 3 --if-not-exists --zookeeper localhost:32181

sudo docker run    --net=host    --rm    confluentinc/cp-kafka:3.1.2    kafka-topics --describe --topic bar --zookeeper localhost:32181


docker run \
   --net=host \
   --rm \
   confluentinc/cp-kafka:3.1.2 \
   kafka-topics --describe --zookeeper 


setup kafka argument, e.g. delete.topic.enable to true, when create container

## install docker on WSL (windows subsystem linux)
https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly

## Setting Up Docker for Windows and WSL
https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly

## install docker on CentOS 
https://github.com/NaturalHistoryMuseum/scratchpads2/wiki/Install-Docker-and-Docker-Compose-(Centos-7)

sudo yum install -y yum-utils device-mapper-persistent-data lvm2 && sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo && sudo yum install -y docker-ce && sudo usermod -aG docker $(whoami) && sudo systemctl enable docker.service && sudo systemctl start docker.service && docker version

## docker run detached with shell
```
docker run -d ...
```

# docker 

## basic concepts
### image
### container
You can think a container is a virtual machine.
### volume

## Dockerfile commands
- FROM tells Docker which image you base your image on (in the example, Python 3).
- RUN tells Docker which additional commands to execute.
- CMD tells Docker to execute the command when the image loads.
```
FROM python:3

ADD my_script.py /

RUN pip install pystrich

CMD [ "python", "./my_script.py" ]
```

## run Dockerfile
### build image
```
docker build --rm=true . --no-cache --file=Dockerfile.server --tag=config-server:latest
```
### create volume
```
docker volume create --name=spring-cloud-config-repo
```
### run the image 
```
docker run --name=config-server config-server:latest
```
you may need --volume=spring-cloud-config-repo:/var/lib/spring-cloud/config-repo to map the directory, https://docs.docker.com/storage/volumes/  
or -p 8080:8080 to map the port

## debug Dockerfile
when generating Dockerfile, you may want to look into the image.  
First, make the image long running by a "tail -f" command at the end of Dockerfile 
```
CMD [ "tail", "-f", "/opt/pyahoo/README.md" ]
```
Then -it bash
```
docker exec -it <container_name> bash
```
Then you see what is inside the docker now. 

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
I found after installed docker windows version, I can run docker in git bash.

## docker remote debug
```
docker run -e "JAVA_OPTS=-agentlib:jdwp=transport=dt_socket,address=5005,server=y,suspend=n" -p 8080:8080 -p 5005:5005 -t springio/gs-spring-boot-docker
```

## docker set environment variable 
docker run -e 
```
docker run -e "SPRING_PROFILES_ACTIVE=prod" -p 8080:8080 -t springio/gs-spring-boot-docker
```

## spring boot dockerization
https://spring.io/guides/gs/spring-boot-docker/  
https://www.baeldung.com/dockerizing-spring-boot-application  

## two docker container visit each other
For example the web module is in one docker container which needs to visit the service docker container.  https://stackoverflow.com/questions/42385977/accessing-a-docker-container-from-another-container  
One way is to use link.  
The second way is to use dockCompose to build the network https://docs.docker.com/compose/gettingstarted/  
https://www.techrepublic.com/article/what-is-the-difference-between-dockerfile-and-docker-compose-yml-files/  
Here is one sample docker-compose.yml
```
version: '3'
services:
  stateapi:
    build: .  # this means it is going to use the Dockerfile on the dir .
    ports:
      - "8080:8080"
  datareader.service:  # the service name
    image: yao/datareader:latest 
    ports:
      - "9090:9090"
```
To run it, docker-compose up
TODO: Now the question comes, the solution above couples stateapi with datareader.service. Let's say I have another service depending on datareader.service and I don't want to restart the dataread.service when stateapi is deployed. How to separate them in deployment and still allow them to visit each other. 

## spring boot kubernetes
Pods, the Pod is the atomic unit of deployment or scheduling in Kubernetes.

## share driver to docker container on windows 10
To get this working in Windows 10 I had to open the Docker Settings window from the system tray and go to the Shared Drives section.

I then checked the box next to C. Docker asked for my desktop credentials to gain authorisation to write to my Users folder.

Then I ran the docker container following examples above and also the example on that settings page, attaching to /data in the container.
'''
docker run -v c:/Users/<user.name>/Desktop/dockerStorage:/data -other -options
'''
You have multiple -v to mount multiple volumes. https://stackoverflow.com/questions/23439126/how-to-mount-a-host-directory-in-a-docker-container  

### mount multiple directories
https://stackoverflow.com/questions/18861834/mounting-multiple-volumes-on-a-docker-container
```
$ docker run -d \
  -it \
  --name devtest \
  --mount type=bind,source="$(pwd)"/target,target=/app \
  --mount type=bind,source="$(pwd)"/target,target=/app2,readonly,bind-propagation=rslave \
  nginx:latest
```

## runtime env variable
```
docker run --env PYAHOO_SYMBOL_FILE=symbol.abc.txt --name=pyahoo pyahoo:latest
```

## docker publish
```
docker login
docker tag <image_name> <tag_name>
docker push <tag_name>
```
The tag name shall be like <dockerhub_username>/<image_name> https://hackernoon.com/publish-your-docker-image-to-docker-hub-10b826793faf 

The commands above are to publish the image to the dockerhub. To pull the image, you only need "docker run...", it checks the local repository first. If it does not exist, it downloads it from dockerhub. 

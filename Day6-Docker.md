# Docker

**What is Docker?**
==> Docker is the open source contanerization tool. Using Docker we can containerized the application and run it on any system without checking for dependency for that applicatio on the system as the container will have all the dependencies required for that particular application.

**Docker engine**: When we install docker docker engine is also installed. dockerd(docker daemon) which runs in background. Docker engine is installed on top of base OS. It provides required resources to application from the base OS. It gives virtual environment.

**Install Docker**


Docker Hub: It has Docker images

Docker File ==> Docker Image ==> Container

Commands:

`docker ps` : To list the running container

`docker ps -a`: To list all the container which are stoped and running

`docker pull mysql` : Pull the image from docker hub. It will download all the thingd required to run mysql like OS,commands, dependencies etc.

`docker run -it --name container_name downloaded_image_name : It will run container from downloaded image 

```
-it=interactive terminal 
-e=environment variable
-d=To run in background
```

`docker run -it --name container_name -e MYSQL_ROOT_PASSWORD=abc@123 downloaded_image_name` : To pass environmental variable.

`docker rm container_id` :To remove/delete the container. Container id we will get by `docker ps -a` command.

`docker exec -it container_id bash`: To acces the container.





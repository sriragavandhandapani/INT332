# Week 5: Advanced Docker Operations (Volumes, Interaction & Real Deployments)

## Topics Covered

* Container Interaction (exec, attach, cp)
* File transfer between host and container
* Volume Management
* Environment Variables
* Real-world Deployments (Nginx, Apache, MySQL)
* Debugging using logs

## Container Interaction Commands

### Execute commands inside container
docker exec -it <container_id> /bin/bash

### Attach to running container
docker attach <container_id>

### Copy files between host and container
**Container → Host**
docker cp <container_id>:/path/file.txt C:\Users\HP\Desktop\

**Host → Container**
docker cp C:\Users\HP\Desktop\file.txt <container_id>:/path/

## File Operations inside Container

mkdir /data
echo "Hello Docker" > /data/test.txt
cat /data/test.txt

## Volume Commands

### Create Volume
docker volume create mydata


### List Volumes
docker volume ls
### Inspect Volume
docker volume inspect mydata

### Use Volume in Container
docker run -dit --name mycontainer -v mydata:/app/data ubuntu


### Remove Volume
docker volume rm mydata
docker volume prune

## Environment Variables
docker run -e ENV_MODE=production nginx

## Logs & Debugging
docker logs <container_name>
docker logs -f <container_name>

## Apache Deployment
docker run -dit --name college_portal -p 8080:80 -e ENV=production -v portaldata:/usr/local/apache2/htdocs httpd

## MySQL Debugging
docker run -d --name mysql_debug -p 3307:3306 \
-e MYSQL_ROOT_PASSWORD=root123 \
-e MYSQL_DATABASE=college \
-e MYSQL_USER=admin \
-e MYSQL_PASSWORD=admin123 mysql:8

## Key Concepts

* `exec` → run commands inside container
* `cp` → transfer files
* volumes → persistent storage
* `logs` → debugging tool
* environment variables → configure container

## Summary

* Containers can be controlled and debugged
* Volumes store persistent data
* Files can be shared between host and container
* Real deployments use environment variables
* Logs help identify issues

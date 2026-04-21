# Week 5: Tasks 

## 🔹 Task 1: Container File Interaction
### Steps
docker exec -it <container_id> ls /
docker exec -it <container_id> /bin/bash
mkdir /data
echo "Hello Docker" > /data/test.txt
exit
docker attach <container_id>
docker cp <container_id>:/data/test.txt C:\Users\HP\Desktop\
docker cp C:\Users\HP\Desktop\test.txt <container_id>:/data/sample.txt
docker exec -it <container_id> ls /data
docker exec -it <container_id> cat /data/sample.txt


## 🔹 Task 2: Project Directory Task
docker exec -it <container_id> mkdir /project
docker exec -it <container_id> sh -c "echo 'Docker Lab Assignment' > /project/report.txt"
docker exec -it <container_id> sh -c "echo 'Container File Creation Test' >> /project/report.txt"
docker exec -it <container_id> cat /project/report.txt
docker cp <container_id>:/project/report.txt C:\Users\HP\Desktop\
docker exec -it <container_id> sh -c "echo 'This is an additional file inside the same container.' > /project/notes.txt"
docker exec -it <container_id> ls /project
docker exec -it <container_id> cat /project/notes.txt


## 🔹 Task 3: Basic Commands
**1. Run nginx with env variable**
docker run -e ENV_MODE=production nginx
**2. View logs**
docker logs -f my_app
**3. Start & Stop container**
docker start web_server
docker stop web_server

## 🔹 Task 4: Volume Basics
docker volume create mydata
docker volume ls
docker volume inspect mydata
docker run -dit --name mycontainer -v mydata:/app/data ubuntu
docker exec -it mycontainer ls /app/data
docker rm -f mycontainer
docker volume rm mydata
docker volume prune

## 🔹 Task 5: Volume Persistence
docker volume create projectdata
docker run -dit --name project_container -v projectdata:/app/data ubuntu
docker exec -it project_container sh -c "echo 'This is my first volume' > /app/data/report.txt"
docker stop project_container
docker rm project_container
docker run -dit --name project_container_new -v projectdata:/app/data ubuntu
docker exec -it project_container_new ls /app/data
docker exec -it project_container_new cat /app/data/report.txt

## 🔹 Task 6: Apache Deployment
docker pull httpd
docker volume create portaldata
docker run -dit --name college_portal -p 8080:80 -e ENV=production -v portaldata:/usr/local/apache2/htdocs httpd
docker exec -it college_portal bash -c "echo '<h1>Welcome to University Portal</h1>' > /usr/local/apache2/htdocs/index.html"
curl http://localhost:8080
docker logs -f college_portal
docker stop college_portal
docker rm college_portal
docker volume rm portaldata
docker rmi httpd



## ✅ Outcome
* File transfer works
* Volumes persist data
* Web server runs successfully
* MySQL container debugged
* Logs used for troubleshooting

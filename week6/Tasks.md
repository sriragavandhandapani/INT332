# Week 6: Tasks

## 🔹 Task 1: Environment Variable

### Steps

docker run -it -e COLLEGE=CSE ubuntu bash
echo $COLLEGE
exit

---

## 🔹 Task 2: Port Mapping

### Run without port mapping

docker run nginx

### Run with port mapping

docker run -p 8080:80 nginx

---

## 🔹 Task 3: Nginx with Environment Variable

docker run -it -p 8080:80 -e NGINX_PORT=8080 nginx

---

## 🔹 Task 4: Apache Deployment

docker pull httpd
docker run -d --name my-apache -p 8080:80 httpd
docker exec -it my-apache bash -c "echo '<h1>Hello from Docker Apache!</h1>' > /usr/local/apache2/htdocs/index.html"
curl http://localhost:8080
docker stop my-apache
docker rm my-apache

---

## 🔹 Task 5: Combined Run Options

docker run -it --name my_app -e APP_ENV=production -v C:\app\data:/data ubuntu /bin/bash
echo $APP_ENV
echo "Hello this is my first program" > /data/test.txt
cat /data/test.txt
echo "Second line added" >> /data/test.txt
cat /data/test.txt

---

## 🔹 Task 6: Container Lifecycle

docker run -d --name my_container nginx
docker stop my_container
docker start my_container

---

## 🔹 Task 7: Logs

docker logs my_container
docker logs -f my_container

---

## ✅ Outcome

* Environment variables are tested
* Port mapping is verified
* Apache server runs successfully
* Multiple docker run options are used
* Container lifecycle commands are executed
* Logs are used for debugging

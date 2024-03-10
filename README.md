# **Two-Tier Flask App and MYSQL Project :smile:**
### In this project we will do containerization of a two-tier application using **Docker, Docker Compose and image scanning with Docker Scout**. We will make a **Flask app and MySQL database.**
___
# Prerequisites
### Before starting the project you should have these things in your system :-
>+ ### Account on AWS
>+ ### Todo App code (we will use code from github repository) [click here for code](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL)
___
## STEP 1: Launch Instance

+ ### Create AWS EC2 instance
![Instance](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/instance.PNG)
+ ### Connect to instance
+ ### After successfully connecting to the EC2 instance, it will look like this
![Connect-Instance](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/instance-connect.PNG)
___
## STEP 2: Install Docker
+ ### First we will update ubuntu system, for this use command as follow :-
```
sudo apt update
```
+ ### Now install docker, for this use command as follow :-
```
sudo apt install docker.io
```
+ ### Now docker is install but if we try command **docker ps** to check running containers then it will show error because current user is not getting to connect docker. So solve this error add your current user to docker group then our user will get all permission of docker, for this use command as follow :-
```
sudo usermod -aG docker $USER
```
+ ### After this once reboot system for this use command as follow :-
```
sudo reboot
```
+ ### Now if we will run **docker ps** it will not show any error.
___
## STEP 3: Clone Code
+ ### To clone code use command as follow :-
```
git clone https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL
```
![Code-Clone](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/code-clone.PNG)
+ ### Now do **ls** and you will see two-tier-appilation folder and go inside this folder, for this use command as follow :-
```
cd 2-Tier-Flask-App-and-MYSQL
```
+ ### Now do **ls** and you will see all files of this folder.
![all-files](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/all-files.PNG)
+ ### Now we will build image. To build image first we will write a Dockerfile and from Dockerfile we will create image. So let's make a dockerfile for this use command :-
```
vim Dockerfile
```
+ ### Now write commands in Dosckerfile as shown in below image :-
![dockerfile](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/dockerfile.PNG)
+ ### Now run command :-
```
docker build . -t flaskapp(image_name)
```
+ ### Now our image is build, to see that image use command :-
```
docker images
```
![Docker-Images](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/docker-images.PNG)
+ ### Now we have to push this image to **dockerhub** so for this use command :-
```
docker login
```
+ ### Enter username and password and we will login from our system to dockerhub. Also we will add tag to our image so for this use command :-
```
docker tag flaskapp sudhajobs0107/flaskapp:latest
```
+ ### Now we will push our image to dockerhub for this use command :-
```
docker push sudhajobs0107(dockerhub_username)/flaskapp:latest
```
+ ### We successfully push our image to dockerhub.
![dockerhub-image](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/dockerhub-image.PNG)
## **Now this image is publically available for everyone. Anyone can pull and run this image.**
## **Now we have 2 options. One is we will make two conatiners and two networks separately one & then another and Second we will make containers and networks by docker-compose file**(*Disclaimer anyone can choose any option.*)


## **<ins>First</ins> :- We will make two container separately one and then another.**
+ ### Now we will make container from images, for this use command as follow :-
```
docker run -d -p 5000:5000 --name flaskapp flaskapp:latest
```
+ ### Now our container is running.
+ ### Now we have to accesss container on port no. 5000. So for this go to **EC2 → Security Groups → Edit inbound rules → add rule (port 5000) → Save Rules**
+ ### Now if we copy Public IPv4 address and paste it in a new tab **Public IPv4 address:5000**. Our application start running, but it will show some errors.
+ ### To run flaskapp we need one mysql container also. So now we will build mysql container for this use command as follow :-
```
docker run -d -p 3306:3306 --name mysql mysql:5.7
```
+ ### Now our containers is running, to see this use command as follow :-
```
docker ps
```
+ ### Now we have to connect both containers through network. So we will create network for this use command as follow :-
```
docker network create twotier
```
+ ### Now we make a network to see network use command as follow :-
```
docker network ls
```
![network](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/network.PNG)
+ ### Now we have to give this network to containers for this first kill above containers (to kill container use command :- **docker kill container_id**) and make new containers with network for this use command as follow :-
```
docker run -d -p 5000:5000 --network twotier --name flaskapp -e MYSQL_USER=root -e MYSQL_HOST=mysql -e MYSQL_DB=KYC -e MYSQL_PASSWORD=test@123 flaskapp:latest

docker run -d --network=twotier --name mysql -e MYSQL_PASSWORD=test@123  -e MYSQL_DATABASE=KYC -e MYSQL_ROOT_PASSWORD=test@123  mysql:5.7
```
+ ### Now our both containers running in same network. To see this use command :-
```
docker network inspect twotier
```
![same-network](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/same-network.PNG)
+ ### Now if we do again **Public IPv4 address:5000** our application show one more error. So solve this error first we have go inside mysql container for this use command :-
```
docker exec -it container_id bash
```
+ ### Now we are inside the container. Now if we do "ls" and it will show all directories.Now type **"mysql -u root -p"** enter your **"password"**. User always be root and password which you put while making container. And we will be inside the **MYSQL**. If we type **"show databases;"** this will show there is a KYC database that we created while making the container. Now to solve our application error we will use our database. So type **"use KYC"** and our database will change. Now copy code from **message.sql** file and paste it in bash.
![showdata](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/showdata.PNG)
+ ### This will create a table. In this table there will be 2 columns :- id and message. Now if we do again **Public IPv4 address:5000** then our application will be running.
![runningapp](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/running-instance.PNG)
+ ### Now if we want to see messages so in bash type **"select * from messages;"** and it will show :-
![table-of-database](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/table-of-database.PNG)

___
## **<ins>Second option</ins> :- Docker-Compose File**
+ ### To make multiple container at once we will use docker-compose file. Now first we have to install docker-compose, for this use command as follow :-
```
sudo apt install docker-compose
```
+ ### Now we will write a docker-compose file, for this use command :-
```
vim docker-compose
```
+ ### Now write all commands inside this docker-compose file as shown in below image :-
![dockercompose-file](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/docker-compose.PNG)
+ ### In this file we added volumes also because if our container will kill then our data will safe.
+ ### Now to run docker-compose file, use command :-
```
docker-compose up -d
```
+ ### Now we create multiple containers in one click. When we build containers in docker-compose file it will automatically create network.
___

### **We deploy our two-tier application through docker.**
___
+ ### To check any vulnerability in our image we can use docker scout.In docker CLI there will not be docker scout preintsall so we have to first install docker scout on our docker CLI. For this first make one directory for this use command :-
```
mkdir ~/.docker/cli-plugins
```
+ ### Now go inside this directory for this use command :-
```
cd /home/ubuntu/.docker/cli-plugins
```
+ ### Now in this directory we will install docker scout plugins for this run command :-
```
curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s --
```
+ ### Now docker scout start running and if we want to scan image that we build in the starting for this run command :-
```
docker scout cves sudhajobs0107/Flaskapp:latest
```
+ ### And this will show us the vulnerabilities.
![dockerscout](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/scout1.PNG)
![dockerscout2](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/scout2.PNG)
+ ### For reference i pushed my Dockerfile and docker-compose.yml on github  for this i did command :-
```
git add Dockerfile
git commit -m "Initial commit"
git push -u origin main
```

___
# **Our Two-Tier Flask App and MYSQL Project is completed :smile:**

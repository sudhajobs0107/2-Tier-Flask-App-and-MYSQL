# **Two-Tier Flask App and MYSQL Project :smile:**
### In this project we will do containerization of a two-tier application using **Docker, Docker Compose and image scanning with Docker Scout**. We will make a **Flask app and MySQL database.**
![project-diagram](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL/blob/main/images/project_diagram.gif)
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
## **Now we have 2 options. One option is we will make two conatiners and one network & then attach that network to containers and Second option we will make containers by docker-compose file and network it will create automatically.**(*Disclaimer anyone can choose any option.*)


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

# **Monitoring Project :smile:**
### In this we will do monitoring using Grafana and Prometheus of our 2-Tier-Flask-App-and-MYSQL Project that we did earlier. If you're interested in exploring my 2-Tier Flask App with MySQL project, to check out my project : [Click here](https://github.com/sudhajobs0107/2-Tier-Flask-App-and-MYSQL.git).
![Architecture](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/monitoring-diagram.gif)

___
# Prerequisites
### Before starting the project you should have these things in your system :-
>+ ### Account on AWS
>+ ### The application should be running
![App running](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/app-running.PNG)

___
# **Part 1** : **Install Prometheus**
+ ### Prometheus we use for metrics & alerting. Now make sure docker.io & docker-compose is installed. Now to configure **Prometheus & cAdvisor** use command as given below [cAdvisor (short for container Advisor) analyzes and exposes resource usage and performance data from running containers. cAdvisor exposes Prometheus metrics out of the box] :-
```
sudo apt-get update
wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml
```
+ ### Now to see prometheus.yml use command :-
```
ls
```

+ ### Now we will make a docker-container.yml to run prometheus, redis & cAdvisor for this write a docker-compose file and run command **docker-compose up -d**.
+ ### Now if we will do **docker ps** we will see our prometheus, cadvisor & redis container is running.
![prom images](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/prom-images.PNG)

+ ### Now we saw prometheus container is running. So copy and paste **Public IPv4 address:9090** in a new tab and you will see :- 
![pro-running](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/pro-running.PNG)
+ ### Now we saw cadvisor container is running. So copy and paste **Public IPv4 address:8080** in a new tab and you will see :- 
![cadvisor](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/cAdvisor.PNG)

+ ### Now we want docker's log and our app container is already added in cAdvisor. So add cAdvisor code in prometheus.yml as given below :-
```
  - job_name: "docker"
    static_configs:
      - targets: ["cAdvisor:8080"]
```
+ ### Now once we have to restart prometheus container so that prometheus.yml will also update inside our prometheous container. To restart use command docker restart container_id.

+ ### Now if we do refresh prometheus page, we will see docker up.
![docker-up](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/docker-up.PNG)
+ ### Now if we want see cpu load average in graph, so Goto Graph → Write → rate(container_cpu_load_average_10s{name="node-app"}[5m]).
![graph](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/graph.PNG)

___
# **Part 2** : Install **Grafana** and set it up to **Work with Prometheus**
+ ### Grafana we use for visualization. Now to install grafana on Ubuntu, use command :-
```
sudo apt-get update
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
+ ### Update the package list and install Grafana, use command :-
```
sudo apt-get update
sudo apt-get install grafana
```
+ ### To enable and Start Grafana Service, use command :-
```
sudo systemctl enable grafana-server
```
```
sudo systemctl start grafana-server
```
+ ### To check Grafana Status, use command :-
```
sudo systemctl status grafana-server
```
![grafana-active](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/grafana-active.PNG)
+ ### Now copy and paste **Public IPv4 address:3000** in a new tab and you will see grafana interface :- 
![login-interface](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/login-interface.PNG)
+ ### Now to login grafana we don't know username & password, so initial username and password for Grafana are username=admin & password=admin.
![dashboard-interface](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/dashboard-interface.PNG)
+ ### Now to visualize Prometheus metrics in Grafana, we have to add prometheus(data source) in grafana. So Goto Connection → Search & Click on "Prometheus" → Click "Add new data source" → In Connection Paste Prometheus URL → Save & Test.

___
## **Prometheus Dashboard**
+ ### Now we will make a dashboard to make it easier to view metrics, you can follow these steps :-

+ ### Click "Dashboard" → Click "Add visualization" → Select "Prometheus" → Select Metric "container_memory_usage_bytes" → Select Name of container "ubuntu_flaskapp_1" → Click "Run queries" → We will a visualization dashboard of our container memory.
 ![dashboard](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/dashboard.PNG)

 + ### Same we can see other metrics also like errors of app container.
 ![dashboard2](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/dashboard2.PNG)
+ ### We've successfully installed and set up Grafana to work with Prometheus for monitoring and visualization.

___
## **Node Exporter**
+ ### Node Exporter we use for to scrape detailed servers/systems metrics. Now we want node exporter in the network of prometheus,so add given code in the prometheus's docker-compose file :-
```
node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    restart: unless-stopped
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    expose:
      - 9100
```
+ ### Now run command **docker-compose up -d** and if we will do **docker ps** we will see our node-exporter container is running.
![node-image](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/node-image.PNG)

+ ### Now to configure Prometheus to scrape metrics from Node Exporter, we need to modify the prometheus.yml file so :-
```
vim prometheus.yml
```
+ ### And in the prometheus.yml file add as given below :-
```
  - job_name: "NodeExporter"
    static_configs:
      - targets: ["node-exporter:9100"]
```
+ ### Now once restart prometheus container.
+ ### Now if we do refresh prometheus page, we will see node-exporter up.
![node-up](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/node-up.PNG)
+ ### Now Prometheus is already added in our connection in Grafana. So to view node metrics, we can import a pre-configured dashboard follow these steps :-
+ ### Click on Dashboard → Click "Import" → Paste id (e.g., code 1860) → Click "Load" → Select data source "Prometheus" → Click "Import".
+ ### Now we will see a Grafana dashboard set up to visualize metrics from Prometheus.
![dashboad3-node-exporter](https://github.com/sudhajobs0107/Monitoring-2-Tier/blob/main/images/dashboard3-node-exporter.PNG)


___
### Our Monitoring Project is completed :smile:.
___

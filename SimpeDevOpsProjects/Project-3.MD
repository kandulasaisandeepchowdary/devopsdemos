## Simple DevOps Project -3 


1. Launch an EC2 instance for Docker host

2. Install docker on EC2 instance and start services 
 
   
--------------Docker installation in rhel/centos-------------------
•	$ sudo yum install docker
•	First remove older version of docker (if any):
$ sudo yum remove docker docker-common docker-selinux docker-engine-selinux docker-engine docker-ce
•	Next install needed packages:
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
•	Configure the docker-ce repo:
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
•	Finally install docker-ce:
$ sudo yum install docker-ce
•	How to enable docker service
$ sudo systemctl enable docker.service

  ```sh 
  yum install docker
  service docker start
  ```

3. create a new user for Docker management and add him to Docker (default) group
```sh
useradd dockeradmin
passwd dockeradmin
usermod -aG docker dockeradmin
```

4. Write a Docker file under /opt/docker

```sh
mkdir /opt/docker

### vi Dockerfile
# Pull base image 
From tomcat:8-jre8 

# Maintainer
MAINTAINER "valaxytech" 

# copy war file on to container 
COPY ./webapp.war /usr/local/tomcat/webapps
```

5. Login to Jenkins console and add Docker server to execute commands from Jenkins  
Manage Jenkins --> Configure system -->  Publish over SSH --> Then add Docker server and credentials (that you have created dockeradmin user before)
![image](https://user-images.githubusercontent.com/85633885/160856504-c034b5bd-aac8-4e8c-a416-6f8ad2b8e6b6.png)
Then goto sudo vi /etc/ssh/sshd_config and
PasswordAuthentication yes and the restart sudo service sshd restart and then test the connection(it will show success)



6. Create Jenkins job 

A) Source Code Management  
 Repository : https://github.com/kandulasaisandeepchowdary/maven-tomcat-project.git  
 Branches to build : */master  

B) Build
 Root POM: pom.xml  
 Goals and options : clean install package  
 
C) send files or execute commands over SSH
 Name: docker_host  
 Source files	: `webapp/target/*.war`
 Remove prefix	: `webapp/target`
 Remote directory	: `//opt//docker`  
 Exec command[s]	: 
  ```sh
 docker stop sai; docker rm -f sai; docker image rm -f sai; cd /opt/docker; docker build -t sai .
  ```
  

  Then give dockeradmin permissions for /opt/docker (chown -R dockeradmin:dockeradmin /opt/docker)
  

D) send files or execute commands over SSH  
  Name: `docker_host`  
  Exec command	: `docker run -d --name valaxy_demo -p 8090:8080 valaxy_demo`  

7. Login to Docker host and check images and containers. (no images and containers)

8. Execute Jenkins job

9. check images and containers again on Docker host. This time an image and container get creates through Jenkins job

10. Access web application from browser which is running on container
```
<docker_host_Public_IP>:8090
```

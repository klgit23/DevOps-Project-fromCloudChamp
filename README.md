# CICD Pipeline
This is a netflix devsecops demo project from cloud champ for learning purpose.
### Steps
* Create an EC2 instance (Ubuntu; T2 Large)
* Install docker      
* Install Git
* Start sonarqube server run in docker container: `docker run -d --name sonar-server -p 9000:9000 sonarqube:lts-community`; correpondingly, open port 9000 in security group

  <img src="https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/76842300-eeee-4cfa-961e-b1155fc27031" width="40%">

* Install Trivy and verify its version
  <img src="https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/48f91bb5-402e-4b53-881a-6e010c1c4590" width="40%">

* Install java: `sudo apt install fontconfig openjdk-17-jre -y`

* Install Jenkins, start jenkins, and open port 8080 in security group
`sudo systemctl enable jenkins`; 
`sudo systemctl start jenkins`

  <img src="https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/c3b378bb-c92b-4dde-a0f0-ea3a3effb10c" width="40%">

* install jenkins plug-ins: manage jenkins->plugins
  - [x] Eclipse Temurin Installer (Install without restart)
  - [x] SonarQube Scanner (Install without restart)
  - [x] NodeJS (Install Without restart)

* Manage jenkins tools: JDK, NodeJS, SonarQube Scanner
* Set up sonarQube server: create a new token (sonarQube: adminstration tab ->security->users->update tokens)
 
   <img src="https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/c76ff570-83bd-470f-ad67-ebd352ad767e" width="40%">

* Inside jenkins->manage jenkins->credential->add a new credential(kind:secret text) ->ID: Sonar-token
  
   ![image](https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/e1caedfb-3132-4467-a07c-7dee4dc00f77)

* Inside jenkins->manage jenkins->system->sonarqube servers->Add sonarqube->server url: pulibic IP of EC2 instance:9000->server authentication token: Sonar-token

  <img src="https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/fe264178-7a94-4533-ac1d-f8863a169736" width="60%" height="75%">

* In sonarqube projection tab ->manually ->create a project (project key)
  ![image](https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/603096a7-9d23-4f90-83d2-fb1685adb02e)

* Set up docker: jenkins dashboard → manage plugins → available plugins → search for Docker and install plugins
  
  ![image](https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/70c843a0-49d2-42f1-ab96-f7a1d5fcdb80)

* Manage jenkins -> Credential(kind:userword and password) -> id: docker
  
  <img src="https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/bb169b89-195f-47ac-b81a-2b58a3e5fb0a" height="50%">

* In tools -> docker installation
  
  ![image](https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/87cfd3c9-518f-451d-9260-9e1d4641de8f)

* Create jenkins pipeline: run `sudo usermod -aG docker jenkins`; in jenkins, new item -> pipeline
  
![image](https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/2a8f5435-a09b-4823-a0cb-d52aa43a5069)
  <img src="https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/56660ba9-5371-42dd-85f6-32ef8eeefdc2" width="50%">

---
---

* My take-1: install github integration plugin in jenkins and add github webhook 
  - In github -> settings -> webhooks -> payload url: http://jenkins-server-url:8080;
  - In jenkins -> check “GitHub hook trigger for GITScm polling” in build trigger
    
  ![image](https://github.com/klgit23/DevOps-Project-fromCloudChamp/assets/154713258/e1dc643b-1f65-4ff6-87b5-4124eebf1e34)

* My take-2: sonarqube webhook and quality gate
  - Log into sonaqube server at EC2 instace IP address:9000 -> administration -> configuration -> webhook -> create -> url: http://jenkins-server-url:8080/sonarqube-webhook/
  - Quality gate: `waitForQualityGate abortPipeline: true, credentialsId: 'Sonar-token'`

* My take-3: use Trivy image scan before pushing image
  - `trivy image --no-progress --exit-code 1 --severity HIGH Account/netflix:latest > trivyimage.txt`






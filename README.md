# Setting Up CICD Life Cycle

- You Tube Tutorials: https://youtu.be/adG0vq5boL8
- GiHub, CICD Java Gradle Application, https://github.com/DeekshithSN/CICD_Java_gradle_application
- Setting Up Instructions https://github.com/DeekshithSN/CICD_Java_gradle_application/wiki
  - Setting Up Sonarqube with Jenkins
  - Creating Docker Hosted Repository in Nexus and Pushing docker image through Jenkins
  - Creating Helm hosted repository in Nexus and Pushing the helm chart
  - Configuring mail Server in Jenkins
  - Configuring PR based trigger in Jenkins
  - How to pull the images from private repository ( nexus ) to Kubernetes Kubeadm cluster
  - Connecting Jenkins with Kubernetes Cluster
- Integrating Sonor Qube - https://github.com/DeekshithSN/CICD_Java_gradle_application/wiki/Integrating-Sonarqube-with-Jenkins
- Installation Scripts - https://github.com/DeekshithSN/cheatsheet/blob/master/installtion_guide_ubuntu.md
 - Java
 - Git
 - Jenkins
 - Maven
 - Docker
 - SonarQube (Docker SonarQUBE)
 - JFROG Artifactory installation in ubuntu 16.04 using docker container
 - Ansible Installation
 - kubernetes installation
 - Nexus Installation
 - Helm Installation
 - installing kubeadm on GCP machines
- Datree - https://hub.datree.io/
- Jenkins Pipeline Syntax - https://www.jenkins.io/doc/book/pipeline/syntax/

## List of Tools required

### Git & Github:
- Git is Client is Client ide Machine, help to organism and push to an pull
- Git Hub is Cloud based SCM Management tool to maintain with version control

### Gradle
- Gradle is build automation tool for Multi Language software development. 
- It Controles development process in the tasks of Compilation and Packaging to testing, deployment, and publishing. 
- Supported languages Java, C / C++ and Javascript
- Faster then Maven and Apache Ant
- Gradle Sonarqube plugin which helps in static code analysis
- Building the Application Code

### SonarQube
  - Statc Code Analysis
  - supports most of the popular languages few of them are Python, C#, Java, JavaScript etc., 
  - Using Gradel Sonarqube plugin. Which helps us in verifying or validating against sonar rules.

### Jenkins
  - Open Source, Continues Integration Continues Deployement
  - Using Plugin we can integrate many tools

### Docker
  - Package an application with all of its dependencis into a standardized usit for software development
  - CICD, We will be writting Dockerfile to Containerize application and create an image out of it and push it to Nexus

### Nexus 
  - Nexus is repository manager allows to store and retrive build artifacts.
  - Mst pupular examples for repository manager are Maven Central Repository, Docker Images, Helm Charts and JCenter at Binary, which we can use to retrive your dependecies for a Maven build & deploying docker images on docker machines and Kuberetes clusters.
  - We will use Nexus to store Docker Iamges and Helm Charts

### Kubernetes
  - Kubernetes are Container Orchestrations tools
  - Deploying our Aplications

### HELM
  - Helm helps you manage Kubernetes Applications
  - Helm Chart help you define, install and upgrade even the most complex Kubernetes applications.
  - We will be using Helm to Deploy application on Kubernetes Cluster
  - Manages Configure Order Process

### datree.io
  - Prevent Kubernetes misconfiguration from reaching production 
  - In CICD we are using datree helm plugin which helps us in identifying misconfiguration in helm charts

## Process Flow
![YIR-CICD-Jenkins-Pipeline-V01](https://user-images.githubusercontent.com/111234771/222896536-6ec1de57-b8aa-4673-bb55-2a54e170d56b.jpg)


## Setting Up Machines
- Jenkins (Ubuntu with 20GB) 
  - Update the Jenkisns to Latest by visiting the Jenkins Website https://updates.jenkins.io/download/war/
  - cd Downloads && wget https://updates.jenkins.io/download/war/2.393/jenkins.war
  - nohup java -jar jenkins.war &
- Nexus
- SonarQube
- Kubernetes Clusters (2 Machines)
  - Master
  - Worker Node


### Configuring Jenkins

### Configuring SonarQube
```
docker run -d -p 9000:9000 sonarqube:lts
```

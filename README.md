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
- Nexus
- SonarQube
- Kubernetes Clusters (2 Machines)
  - Master
  - Worker Node


### Configuring Jenkins
  - Update the Jenkisns to Latest by visiting the Jenkins Website https://updates.jenkins.io/download/war/
  - cd Downloads && wget https://updates.jenkins.io/download/war/2.393/jenkins.war
  - nohup java -jar jenkins.war &
  - Visit using URL http://localhost:8080
  - Username: y_ijohn
  - Password: Divya@1234
  - Full Name: Irudaya Raj John Y
  - Email: y_ijohn@yahoo.com

#### Downgrading Jenkns to 2.303.2
```
sudo systemctl stop jenkins
wget http://updates.jenkins-ci.org/download/war/2.303.2/jenkins.war
sudo mv jenkins.war /usr/share/jenkins/
sudo chown jenkins:jenkins /usr/share/jenkins/jenkins.war
sudo systemctl start jenkins
sudo cat /var/lib/jenkins/config.xml | grep -i "version"
java -jar /usr/share/jenkins/jenkins.war --version

```

#### Uninstalling Jenkins
```
sudo apt-get remove -y jenkins
sudo apt-get -y autoremove

sudo rm -rf /var/lib/jenkins
sudo rm -rf /var/cache/jenkins
sudo rm -rf /var/log/jenkins

sudo userdel jenkins
sudo groupdel jenkins

```
#### Install Jenkins with Specific Version
```
sudo apt update
sudo apt install -y openjdk-8-jdk
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
echo deb https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list
sudo apt update
sudo apt install -y jenkins=2.89.4
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
#### Setting Up Docker and Jenkins Permission
- The user jenkins needs to be added to the group docker
```
sudo usermod -a -G docker jenkins
```
#### Option 3 - Setting up Jenkins and SonarQube with Docker-Compose
- Create a file docker-compose.yml
```
version: "3"
services:
  sonarqube:
    image: sonarqube:lts
    ports:
      - 9000:9000
    networks:
      - mynetwork
    environment:
      - SONAR_FORCEAUTHENTICATION=false
  jenkins:
    build:
      context: .
    ports:
      - 8080:8080
    networks:
      - mynetwork
networks:
  mynetwork:
```
- Run the following command to run Docker Services in the file
```
Run `docker-compose up -d --build`
```
- you'll have access to:
  - Jenkins at http://localhost:8080
  - SonarQube at http://localhost:9000


### Configuring SonarQube
#### Option 1 - SonarQube Minimum Setup 
```
docker run -d -p 9000:9000 sonarqube:lts
docker run -itd --restart=always -p 9000:9000 sonarqube:lts # Adding Policy to the docker container
```
- Visit using URL http://localhost:9000
  - Username: admin
  - Password: Divya@1234
  - Email: y_ijohn@yahoo.com

#### Option 2 - With Dat Volume attached to SonarQube
- Create a file ***sonarqube_setup.sh*** and add the below content 
```
#!/bin/bash

# Create a Docker volume for SonarQube data
docker volume create sonarqube_data

# Start SonarQube container with data volume
docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 -v sonarqube_data:/opt/sonarqube/data sonarqube

# Wait for SonarQube to start up
echo "Waiting for SonarQube to start up..."
until $(curl --output /dev/null --silent --head --fail http://localhost:9000/api/system/status); do
    printf '.'
    sleep 5
done
echo "SonarQube is up and running!"

# Attach data volume to retain data and settings
docker stop sonarqube
docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 -v sonarqube_data:/opt/sonarqube/data sonarqube

echo "SonarQube container is now running with data volume attached."

```
- Setting Up
```
chmod +x sonarqube_setup.sh
./sonarqube_setup.sh

```

### Nexus
- Visit using URL http://localhost:8081
  - Username: admin
  - Password: Divya@1234
  - Email: y_ijohn@yahoo.com

### Setting up Kubernetes

### Setting up Helm on jenkins
#### - Installation
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
#### Uninstall
```
which helm ( to see which folder its installed )
rm -rf /usr/local/bin/helm
```

### Integrations of Datree.io on jenkins
helm plugin install https://github.com/datreeio/helm-datree
- https://github.com/datreeio/helm-datree

<hr>

## Application Setup

### Adding Plugins for SonarQube
- SonarQube Scanner
- Gerrit Trigger
- Sonar Gerrit
- SonarQube Generic Coverage
- Quality Gates
- Sonar Quality Gates

### Adding Plugins for Docker
- Docker
- Docker Pipeline
- docker-build-step

### Adding SonarQube Configuration
#### Generating Token in Sonar Qube
- Goto SonarQube
  - Select Administration
    - Select Security
      - User, Select Tokens
        - Provide the Valid Name and Copy the Token Secret Key 
          - squ_6af6f7534ce43134a4c406feabe7141a49c9b4a5
          
#### Jenkins Credential Setting of Sonar Qube
  - Go to Dashboard 
   - Go to Manage Jenkins
    - Go to Credentials, Select Global
     - elect the Kind to <Secret Text>

  
#### Setting SonarQube Environment on Jenkins Pipelines
  - Select the Project Name
    - Select Pipeline Syntax
     - Select withSonarQubeEnv: Prepare SonarQube Scanner Environment
      - Select Server Quthetication Token
      - Click on Generate Pipeline Script
  
  _View Result_
  ![image](https://user-images.githubusercontent.com/111234771/222962821-65a63110-1b1b-45c5-b852-b46bbdd06347.png)

  #### Setting up Commit Based Job in Jenkins
  - Go to New Item, provide Proper Name, Select Pipeline Job
    - Tick Mark on GitHub hook trigger for GITScm polling Under Build Triggers Section
    - Go to Pipeline Section, Select Pipeline Script from SCM
      - Select Git form the Dropdown List Under SCM
      - 
  

#### Setting Up GitHub Personal Access Token with HTTPS - Private Repository
  - Go to User Profile, Select Settings
    - Go to Developer Settings
      - Select Personal Access Token
        - Select Tokens (Classic), Click on Generate New Token
          - Tick -  repo Full control of private repositories
          - Tick - read:org Read org and team membership, read org projects
          - Tick -  user:email Access user email addresses (read-only)
          - Generte and Copy the token 
          ```
          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
          ```
          ```
          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
          ```
  ```
  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
  ```
  ```
    https2 - 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
  ```
  <hr>
  
#### Setting Up GitHub SSH Token
  ```
  ssh-keygen -t ed25519 -C "yir.john@simpragma.com"
  ```
  ```
  cat ~/.ssh/id_ed25519.pub
  ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOf9dU+iztmqI0j6PgfTWL8ljVLELUCoVah4wpWBOaRl yir.john@simpragma.com
  ```
  - Go to Settings
    -  SSH and GPG Keys and the Pub key from the Machine
  
  - Setup up Private Key in the Jenkins
    - Go to Terminal, 
      - cat ~/.ssh/id_ed25519
  ![image](https://user-images.githubusercontent.com/111234771/223159399-09bfbb81-e438-450a-851e-b490ed348265.png)


# 10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application
A step-by-step guide to deploying a Java-based Pet Clinic application on Tomcat Server using Jenkins as a CI/CD tool
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/c2967144-020b-4cab-95bc-3694fa410193)

we will be deploying a Pet Clinic Java Based Application. This is an everyday use case scenario used by several organizations. We will be using Jenkins as a CICD tool and deploying our application on Tomcat Server. Hope this detailed blog is useful.

We will be deploying our application in two ways, using Docker Container and other is using Tomcat Server.

Steps:-

Step 1 — Create an Ubuntu T2 Large Instance

Step 2 — Install Jenkins, Docker and Trivy. Create a Sonarqube Container using Docker.

Step 3 — Install Plugins like JDK, Sonarqube Scanner, Maven, OWASP Dependency Check,

Step 4 — Create a Pipeline Project in Jenkins using Declarative Pipeline

Step 5 — Install OWASP Dependency Check Plugins

Step 6 — Docker Image Build and Push

Step 7 — Deploy image using Docker

Step 8 — Install Tomcat on Port 8083 and finally deploy on Apache Tomcat using groovy pipeline script mentioned

Step 9 — Access the Real World Application

Step 10 — Terminate the AWS EC2 Instance

References

Now, lets get started and dig deeper into each of these steps :-

Step 1 — Launch an AWS T2 Large Instance. Use the image as Ubuntu. You can create a new key pair or use an existing one. Enable HTTP and HTTPS settings in the Security Group.
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/2e02c791-2656-4f1b-bdec-cb01b56b7899)


Step 2 — Install Jenkins, Docker and Trivy

2A — To Install Jenkins

Connect to your console, and enter these commands to Install Jenkins

sudo apt-get update

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
    /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install openjdk-17-jdk
sudo apt install openjdk-17-jre

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

sudo cat  /var/lib/jenkins/secrets/initialAdminPassword
Once Jenkins is installed, you will need to go to your AWS EC2 Security Group and open Inbound Port 8080, since Jenkins works on Port 8080.

![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/69fe3a48-6449-4fa5-aa55-b726b215c3c2)


Now, grab your Public IP Address

<EC2 Public IP Address:8080>
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Unlock Jenkins using an administrative password and install the required plugins.
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/d8cd98f1-31ca-4d7b-8363-201755c9418f)


Jenkins will now get installed and install all the libraries.
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/dad76ae1-10b2-4fc1-a821-ba96fcd0f967)


Jenkins Getting Started Screen
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/ba4a717a-38a1-47c5-8046-d9d07090d0be)


2B — Install Docker

sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
sudo chmod 777 /var/run/docker.sock 
sudo docker ps
After the docker installation, we create a sonarqube container (Remember added 9000 port in the security group)
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/8440b822-315e-4e13-91b4-6edc45746c94)


docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/6d6eaa70-6af9-4090-bd61-6fc79caa3860)

![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/272f0241-74ae-48d5-8ddd-90b4f3f7efca)

![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/cf8a475f-08e2-486a-915e-65c57e6968d1)

2C — Install Trivy

sudo apt-get install wget apt-transport-https gnupg lsb-release

wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

sudo apt-get update

sudo apt-get install trivy -y
Next, we will login to Jenkins and start to configure our Pipeline in Jenkins

Step 3 — Install Plugins like JDK, Sonarqube Scanner, Maven, OWASP Dependency Check,

3A — Install Plugin

Goto Manage Jenkins →Plugins → Available Plugins →

Install below plugins

1 → Eclipse Temurin Installer (Install without restart)

2 → SonarQube Scanner (Install without restart)

3B — Configure Java and Maven in Global Tool Configuration

Goto Manage Jenkins → Tools → Install JDK and Maven3 → Click on Apply and Save

3C — Create a Job

Label it as Real-World CI-CD, click on Pipeline and Ok.
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/085fb1a4-5810-4b64-a3c2-d4bcb6d6e36d)


Enter this in Pipeline Script,

pipeline {
    agent any 
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
     stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/writetoritika/Petclinic.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
     }
}
The stage view would look like this,
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/070e0f4f-d04f-47d8-a7d2-f4d22e024ad6)


Step 4 — Configure Sonar Server in Manage Jenkins

Grab the Public IP Address of your EC2 Instance, Sonarqube works on Port 9000 , sp <Public IP>:9000. Goto your Sonarqube Server. Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/f69a7bd1-b565-4aac-a549-c80c65b9b73c)


Click on Update Token
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/070149d3-5f9a-4b30-b8da-59440cb14a30)


Copy this Token

Goto Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/a06d04db-47aa-4d96-8636-6e894c492e3d)


Now, goto Dashboard → Manage Jenkins → Configure System
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/5042f94e-a2e9-4765-8332-30fcde41ca87)


Click on Apply and Save

Configure System option is used in Jenkins to configure different server

Global Tool Configuration is used to configure different tools that we install using Plugins

We will install sonar-scanner in tools.
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/13a57c56-5b02-446a-a8e7-c0427b0d8d31)


Lets goto our Pipeline and add Sonar-qube Stage in our Pipeline Script

pipeline {
    agent any 
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    
    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/writetoritika/Petclinic.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
        
        }
     }
}
Click on Build now, you will see the stage view like this
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/5a370098-958f-48fa-82ef-40a19f653654)


To see the report, you can goto Sonarqube Server and goto Projects.
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/cb9b0548-93e9-40b0-8b6c-3c6395e6b6d0)


You can see the report has been generated and the status shows as passed. You can see that there are 15K lines. To see detailed report, you can go to issues.

Step 5 — Install OWASP Dependency Check Plugins

GotoDashboard → Manage Jenkins → Plugins → OWASP Dependency-Check. Click on it and install without restart.
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/8eb8808f-fbf4-4926-a2a6-5073b8138e53)


First, we configured Plugin and next we have to configure Tool

Goto Dashboard → Manage Jenkins → Tools →
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/e4faa342-efc0-4093-ae47-46b1577a7843)


Click on apply and Save here.

Now goto configure → Pipeline and add this stage to your pipeline

   stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
The final pipeline would look like this,

pipeline {
    agent any 
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
     environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/writetoritika/Petclinic.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
    
                }
            }
        }
        
      
        stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
        
          stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ ' , odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
    }
}
The stage view would look like this,
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/4d02bd18-a4b1-4bd8-b3a4-63426aec31ab)


You will see that in status, a graph will also be generated
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/94db410d-2b09-4e6b-b009-50d1ec1c7d3b)


Step 6 — Docker Image Build and Push

We need to install Docker tool in our system, Goto Dashboard → Manage Plugins → Available plugins → Search for Docker and install these plugins

Docker
Docker Commons
Docker Pipeline
Docker API
docker-build-step
and click on install without restart

Now, goto Dashboard → Manage Jenkins → Tools →
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/344a6de3-b865-44b3-8c6e-3cca36f2c354)


Add DockerHub Username and Password under Global Credentials
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/fb4ae866-37d9-430d-99c6-878c42b7ca84)


Add this stage in Pipeline Script

    stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: ''bc86df08-bacf-4695-99cb-8cefb3406235', toolName: 'docker') {
                        
                        sh "docker build -t petclinic1 ."
                        sh "docker tag petclinic1 writetoritika/pet-clinic123:latest "
                        sh "docker push writetoritika/pet-clinic123:latest "
                    
                    }
                }
            }
        }
You will see the output like below,
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/e2450831-4ba1-43a6-aa9c-5c0e54df0d11)
\Now, when you do

docker images
You will see this output
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/3ab5a362-caba-4755-b585-6fb94ee35ba3)


When you log in to Dockerhub, you will see a new image is created
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/0ee36c9d-8220-4a48-8c03-13504d34a8f5)


Step 7 — Deploy image using Docker

Add this stage to your pipeline syntax

stage("Deploy Using Docker"){
            steps{
                sh " docker run -d --name pet1 -p 8082:8082 writetoritika/pet-clinic123:latest "
            }
        }
You will see the Stage View like this,
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/30e4b236-f6e5-4f0f-9979-a1b4277b6f8a)


Step 8 — Install Tomcat on Port 8083 and finally deploy on Apache Tomcat using groovy pipeline script mentioned

Before we add Pipeline Script, we need to install and configure Tomcat on our server.

Here are the steps to install Tomcat 9

cd /opt/
sudo wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.65/bin/apache-tomcat-9.0.65.tar.gz
sudo tar -xvf apache-tomcat-9.0.65.tar.gz
sudo rm -rf apache-tomcat-9.0.65
sudo su

cd /opt/apache-tomcat-9.0.65/conf
sudo vi tomcat-users.xml
---add-below-line at the end (2nd-last line)----
<user username="admin" password="admin1234" roles="admin-gui, manager-gui"/>



sudo ln -s /opt/apache-tomcat-9.0.65/bin/startup.sh /usr/bin/startTomcat
sudo ln -s /opt/apache-tomcat-9.0.65/bin/shutdown.sh /usr/bin/stopTomcat



sudo vi /opt/apache-tomcat-9.0.65/webapps/manager/META-INF/context.xml
comment:
  <!-- Valve className="org.apache.catalina.valves.RemoteAddrValve"
  allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
  
sudo vi /opt/apache-tomcat-9.0.65/webapps/host-manager/META-INF/context.xml
comment:
  <!-- Valve className="org.apache.catalina.valves.RemoteAddrValve"
  allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->



sudo stopTomcat
sudo startTomcat
SincePort 8080 is being used by Jenkins, we have used Port 8083 to host Tomcat Server
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/14ccfd32-eb0b-4a1f-aecf-99c50eb659c1)


Add this stage to your Pipeline script

  stage("Deploy To Tomcat"){
            steps{
                sh "cp  /var/lib/jenkins/workspace/Real-World-CI-CD/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/ "
            }
        }
Kindly note that this application can be deployed via Docker and also via Tomcat Server. Here I have used Tomcat to deploy the application

The final script looks like this,

pipeline {
    agent any 
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
     environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/writetoritika/Petclinic.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
       
        
      
        stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
        
          stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ ' , odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
       
        stage("Docker Build & Push"){
            steps{
                script{
                        withDockerRegistry(credentialsId: 'bc86df08-bacf-4695-99cb-8cefb3406235', toolName: 'docker') {
                  
                        
                             sh "docker build -t petclinic1 ."
                             sh "docker tag petclinic1 writetoritika/pet-clinic123:latest "
                             sh "docker push writetoritika/pet-clinic123:latest "
                    
                    }
                }
            }
        }
        
        stage("Deploy Using Docker"){
            steps{
                sh " docker run -d --name pet12 -p 8082:8082 writetoritika/pet-clinic123:latest "
            }
        }
        
        stage("Deploy To Tomcat"){
            steps{
                sh "cp  target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/ "
            }
        }
    }
}
And you can access your application on Port 8083. This is a Real World Application that has all Functional Tabs.
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/b414132b-e66f-4f38-807a-d4d53d40eebc)


Step 9 — Access the Real World Application
![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/c82f7057-c940-427c-acf0-3c18d87775a8)

![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/a696873d-74c0-4630-8898-b14458b4f5c6)

![image](https://github.com/fazalUllah-Khan/10-Stages-Real-World-CI-CD-DevSecOps-Pipeline-for-Deployment-of-Petclinic-Application/assets/148821704/dcac6102-48d4-48fd-819c-f288233924c0)


Step 10 — Terminate the AWS EC2 Instance

Lastly, do not forget to terminate the AWS EC2 Instance.

Resources Used — https://www.youtube.com/watch?v=Hen2biH2BSI

Reference: 
https://github.com/fazalUllah-Khan/Petclinic 
https://github.com/writetoritika/Petclinic 

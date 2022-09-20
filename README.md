# helloworld
## Install infrastructure

### 1. Create docker images
```
cd Agent 
docker build -t gccagent .
cd ..
```
### 2. Run docker compose 
```
cd jenkins-compose
docker-compose -f jenkins-agent-docker-compose.yml up -d
```

## Setup jenkins

### 1. Get password
```
docker exec -it jenkins bash
cat var/jenkins_home/secrets/initialAdminPassword
exit
```
### 2. Go to link and paste password. Install all plugins and fill in the login details(after only next next to main page)
```
http://localhost:8080
```

### 3. Connect to agent 
```
Dashboard -> Manage Jenkins -> Nodes -> New Node
```
Node name: random
Create

* Remote root directory:/home/jenkins/agent
* Labels: gcc gccAgent
* Launch method: Launch agents via SSH
* Host:agent-ubuntu (connected to container name in docker compose)

New credentials:
* create new 
* Kind: Username with password
* Username:jenkins(connected to dockerfile line 49)
* Password:jenkins(connected to dockerfile line 49)
* ID:jenkins
* Description:jenkins username and password
* Create a and use in drop down
* Host Key Verification Strategy:Non verifying Verification Strategy

Continue with button Advanced
* Port: 22
* JavaPath: /usr/local/jdk-11/bin/java (this is important and we can check in cli via)
```
docker exec -it agent-ubuntu bash 
cd usr/local/jdk-11/bin/
ls 
```
* Save 
* By our agent icon there is a small down arrow use it and select logs
* If we get last message:"Agent successfully connected and online " is agent done

### 4. Add credential for artifactory
```
Dashboard -> Manage Jenkins -> Manage credential -> (global) -> Add credential
```
New credentials:
* create new 
* Kind: Username with password
* Username:admin(is default on artifactory)
* Password:password(is default on artifactory)
* ID:credential-for-artifactory
* Description:credential-for-artifactory
* Save 

### 5. Create job
```
Dashboard -> New item
```
* Enter an item name: HelloWorld
* Choice:pipeline
* click OK
* GitHub project->Project url:https://github.com/Ambux5/helloworld.git
* Copy Pipeline script from jenkinsfile
* Save
* Build Now

## resources 
https://github.com/u-boot/jenkins-slave-ubuntu/blob/master/Dockerfile

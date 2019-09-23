#!/usr/bin/env groovy
 
/**
        * Sample Jenkinsfile for SpringBoot-Chat Application Pipeline
        * from https://github.com/praveenkumarn/spring-boot-websocket-chat-Kube/edit/master/Jenkinsfile
        * by Praveen
 */


timestamps {

node ('Kubernetes') { 

	stage ('K8s_BnD - Checkout') {
 	 checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'GitHub', url: 'https://github.com/praveenkumarn/spring-boot-websocket-chat-demo']]]) 
	}
	stage ('K8s_BnD - Build') {
 	
	withMaven(maven: 'maven') { 
 			if(isUnix()) {
 				sh "mvn -f pom.xml clean package " 
			} else { 
 				bat "mvn -f pom.xml clean package " 
			} 
 		}		// Shell build step
sh """ 
#!/bin/bash
pwd
id
ls -lrt
java -version
hostname

sudo -S docker image ls
sudo -S docker container ls

sudo -S kubectl get deployment

sudo -S kubectl get services

sudo -S kubectl delete services kubernetes-springboot

sudo -S kubectl delete -n default deployment kubernetes-springboot

sudo -S docker build -t spring-boot-websocket-chat-demo .

sudo -S docker image ls

sudo -S docker tag spring-boot-websocket-chat-demo praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT
cat ~/pass.txt | sudo -S docker login --username praveenkumarnagarajan --password-stdin
sudo -S docker push praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT 
sudo -S docker pull praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT

sudo -S docker image ls
sudo -S kubectl run kubernetes-springboot --image=praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT --port=8080
sudo -S kubectl expose deployment/kubernetes-springboot --type="NodePort" --port 8080

sudo -S kubectl get nodes

sudo -S kubectl get services

sudo -S kubectl describe services/kubernetes-springboot


 """ 
 cleanWs()
	}
}
}

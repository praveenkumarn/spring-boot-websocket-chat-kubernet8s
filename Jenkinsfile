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
 	
	withMaven(maven: 'M2_HOME') { 
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

docker image ls
docker container ls

kubectl get deployment
kubectl get services

#kubectl delete services kubernetes-springboot
#kubectl delete -n default deployment kubernetes-springboot


docker build -t spring-boot-websocket-chat-demo .
docker image ls

sudo -S docker tag spring-boot-websocket-chat-demo praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT
cat ~/pass.txt |  docker login --username praveenkumarnagarajan --password-stdin
docker push praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT 
docker pull praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT

docker image ls
kubectl run kubernetes-springboot --image=praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT --port=8080
kubectl expose deployment/kubernetes-springboot --type="NodePort" --port 8080

kubectl get nodes
kubectl get services
kubectl describe services/kubernetes-springboot


 """ 
 cleanWs()
	}
}
}

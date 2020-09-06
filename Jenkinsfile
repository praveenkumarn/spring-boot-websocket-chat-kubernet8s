#!/usr/bin/env groovy
 
/**
        * Sample Jenkinsfile for SpringBoot-Chat Application Pipeline
        * from https://github.com/praveenkumarn/spring-boot-websocket-chat-Kube/edit/master/Jenkinsfile
        * by Praveen
 */


pipeline {
agent { label 'Kube8Agent' }
stages {
	stage ('KGL_Complete_CI - Checkout') {
	steps {
 	 checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '00a9b575-7866-4f8b-9995-6ea0281fa5b8', url: 'http://gitlab.cmtcde.com/root/spring-boot-websocket-chat-Kube.git']]]) 
	}
	}
	stage ('KGL_Complete_CI - Build') {
 	steps {
 	withSonarQubeEnv('SonarQube') {
	withMaven(maven: 'M2_HOME')  { 
 		 				sh "mvn -f pom.xml clean verify org.apache.maven.plugins:maven-jxr-plugin:2.5:jxr org.apache.maven.plugins:maven-pmd-plugin:3.10.0:pmd org.apache.maven.plugins:maven-pmd-plugin:3.10.0:cpd org.apache.maven.plugins:maven-checkstyle-plugin:3.0.0:checkstyle org.codehaus.mojo:findbugs-maven-plugin:3.0.1:findbugs"
                sh "mvn -f pom.xml test org.codehaus.mojo:cobertura-maven-plugin:2.7:cobertura -Dcobertura.report.format=xml"
                sh "mvn -f pom.xml package"
                sh "mvn sonar:sonar"
                sh "sleep 60"
				}		
	}
 	}
}
        stage('KGL_Complete_CI-Quality Gate') {
            steps {
            timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
}
}
        }
 stage ('KGL_Complete_CI - Docker-Build') {
    steps {
// Shell build step
sh label: '', script: '''#!/bin/bash
pwd
id
ls -lrt
java -version
process_count=`kubectl get services | grep chat-server | grep -v grep | wc -l`
if [ "${process_count}" -eq "0" ] ; then
     echo "chat-server not running.No action required"
else
     echo "kubernetes-springboot services running, so delete the services and deployment"
    	kubectl delete services chat-server
        kubectl delete -n default deployment chat-server
fi


docker image ls
docker container ls

docker build -t spring-boot-websocket-chat-demo .

docker tag spring-boot-websocket-chat-demo praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT

cat ~/pass.txt | docker login --username praveenkumarnagarajan --password-stdin

docker push praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT'''
		// Checkstyle report
		step([$class: 'CheckStylePublisher', canComputeNew: false, defaultEncoding: '', healthy: '90', pattern: '**/checkstyle-result.xml. ', unHealthy: '40']) 
	}
}

stage ('KGL_Complete_CI-Deploy') {
    steps {
// Shell build step
sh """ 
#!/bin/bash
pwd
id
ls -lrt
cat ~/pass.txt | docker login --username praveenkumarnagarajan --password-stdin

docker pull praveenkumarnagarajan/spring-boot-websocket-chat-demo:0.0.1-SNAPSHOT

docker image ls
kubectl get nodes
kubectl get services
kubectl apply -f k8s-deployment.yaml

kubectl get nodes
kubectl get services
kubectl describe services/chat-server """
 cleanWs()
}
}
}
}

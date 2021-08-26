pipeline {
    agent any
        environment {
            AWS_ACCESS_KEY_ID     = credentials ('AWS_ACCESS_KEY_ID')
            AWS_SECRET_ACCESS_KEY = credentials ('AWS_SECRET_ACCESS_KEY')
            AWS_DEFAULT_REGION    = credentials ('AWS_DEFAULT_REGION')
        } 
    stages {
        // stage('Download Direction-App code') {
        //     steps {
        //         // Get direction App code from the repository
        //         git 'https://github.com/adesoji-blick/Direction-App.git'
        //         // current working directory
        //         sh "pwd"
        //         // List current contents.
        //         sh "ls -ltr"
        //     }
        // }
        stage('Build Docker Image for Direction-App') {
            steps {
                // Building Docker Image for Direction App
                sh "sudo docker build -t direction-app:latest ."        
            }
        }
        stage('Manage Master Branch for Prod App') {
            when {
                branch "master"
            }
            steps {
                // tag docker image for prod app and push to docker.io
                sh "sudo docker tag direction-app:latest blickng/direction-app-prod:latest"
                withCredentials([string(credentialsId: 'DockerUserID', variable: 'dockerusername'), string(credentialsId: 'DockerPassword', variable: 'dockerpassword')]) {
                sh "sudo docker login -u blickng -p $dockerpassword"
                sh "sudo docker push blickng/direction-app-prod:latest"
                sh "sudo docker logout"
                }   
                // ssh into prod machine Pull docker image and run container instance in remote machine
                withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-key', keyFileVariable: '')]) {
                sh "ssh ec2-user@35.182.252.41 sudo docker rm -f direction-app-prod"     
                sh "ssh ec2-user@35.182.252.41 sudo docker run -d -p 8080:8080 -e loginname=myname -e loginpass=mypass -e api_key=xxxxxxxx --name direction-app-prod blickng/direction-app-prod:latest"
             }
           }
        }
        stage('Manage Develop Branch for Test App') {
            when {
                branch "develop"
            }
            steps {
                // tag docker image for dev app and push to docker.io
                sh "sudo docker tag direction-app:latest blickng/direction-app-dev:latest"
                withCredentials([string(credentialsId: 'DockerUserID', variable: 'dockerusername'), string(credentialsId: 'DockerPassword', variable: 'dockerpassword')]) {
                sh "sudo docker login -u blickng -p $dockerpassword"
                sh "sudo docker push blickng/direction-app-dev:latest"
                sh "sudo docker logout"
                }   
                // ssh into dev machine Pull docker image and run container instance in remote machine
                withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-key', keyFileVariable: '')]) {
                sh "ssh ec2-user@99.79.10.86 sudo docker rm -f direction-app-dev"     
                sh "ssh ec2-user@99.79.10.86 sudo docker run -d -p 8080:8080 -e loginname=myname -e loginpass=mypass -e api_key=xxxxxxxx --name direction-app-dev blickng/direction-app-dev:latest"
             }
           }
        }
    }
}
pipeline {
    agent any
        environment {
            AWS_ACCESS_KEY_ID     = credentials ('AWS_ACCESS_KEY_ID')
            AWS_SECRET_ACCESS_KEY = credentials ('AWS_SECRET_ACCESS_KEY')
            AWS_DEFAULT_REGION    = credentials ('AWS_DEFAULT_REGION')
        } 
    stages {
        stage('Direction App Code Download') {
            steps {
                // Get direction App code from the repository
                git 'https://github.com/adesoji-blick/Direction-App.git'
                // current working directory
                sh "pwd"
                // List current contents.
                sh "ls -ltr"
            }
        }
        stage('Build Docker Image') {
            steps {
                // Building Docker Image for Direction App
                sh "sudo docker build -t direction-prod:latest ."
                sh "sudo docker tag direction-prod:latest blickng/direction-prod:latest"
                withCredentials([string(credentialsId: 'DockerUserID', variable: 'dockerusername'), string(credentialsId: 'DockerPassword', variable: 'dockerpassword')]) {
                sh "sudo docker login -u blickng -p $dockerpassword"
                sh "sudo docker push blickng/direction-prod:latest"
                sh "sudo docker logout"
                }            
            }
        }
        stage('Master Branch') {
            when {
                branch "master"
            }
            steps {
                // ssh into prod, machine Pull docker image and run container instance in remote machine
                withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-key', keyFileVariable: '')]) {
               sh "ssh ec2-user@35.182.252.41 sudo docker run -d -p 8080:8080 -e loginname=myname -e loginpass=mypass -e api_key=xxxxxxxx blickng/direction-prod:latest"
             }
           }
        }
        stage('Develop Branch') {
            when {
                branch "develop"
            }
            steps {
                // ssh into dev, machine Pull docker image and run container instance in remote machine
                withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-key', keyFileVariable: '')]) {
               sh "ssh ec2-user@99.79.10.86 sudo docker run -d -p 8080:8080 -e loginname=myname -e loginpass=mypass -e api_key=xxxxxxxx blickng/direction-dev:latest"
             }
           }
        }
    }
}
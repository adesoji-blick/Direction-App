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
    // some block
                sh "sudo docker login -u blickng -p $dockerpassword"
                sh "sudo docker push blickng/direction-prod:latest"
                sh "sudo docker logout"
}            
            }
        }
        stage('Pull docker image and run container instance') {
            steps {
                // ssh into prod machine
                withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-key', keyFileVariable: '')]) {
    // some block
               sh "ssh ec2-user@35.182.252.41 sudo docker run -d -p 8080:8080 blickng/direction-prod:latest -e loginname=ade -e loginpass=pass -e api_key=xxxxxxxx --name dir_app"
}
                
            }
        }
        // stage('Get Approval') {
        //     steps {
        //        script {
        //           def userInput = input(id: 'confirm', message: 'Apply Terraform?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Apply Terraform', name: 'confirm'] ]) 
        //        }
        //     }
        // } 
        // stage('Apply Terraform Code') {
        //     steps {
        //         // Run terraform apply
        //         sh "terraform apply --auto-approve"
        //     }
        // }
    }
}
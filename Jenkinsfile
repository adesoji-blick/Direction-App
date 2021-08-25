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
          }
        }
        // stage('Run Terraform Plan') {
        //     steps {
        //         // Run terraform plan
        //         sh "terraform plan"
        //     }
        // }
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
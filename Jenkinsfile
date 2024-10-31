pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }

    environment {

        APP_NAME   = "cart-app"
        IMAGE_NAME = "waldara"
        IMAGE_TAG  = "1.0"

    }
    
    stages {
        stage('Clean WorkSpace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Build') {
            agent { docker 'maven:3.8.6-openjdk-8' }
            steps {
                git branch: 'main', url: 'https://github.com/waldra/Petclinic.git'
                sh 'mvn clean package -DskipTests=true'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}/${APP_NAME}:${IMAGE_TAG}")
                }
            }
        }
    }
}

pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Clean WorkSpace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/waldra/shopping-cart.git'
            }
        }

        stage('Compile') {
            agent { docker 'maven:3.8.6-openjdk-8' }
            steps {
                git branch: 'main', url: 'https://github.com/waldra/shopping-cart.git'
                sh 'mvn clean test'
            }
        }
        
        stage('Code Quality Check') {
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh ''' ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=shopping-card \
                    -Dsonar.projectKey=shopping-card \
                    -Dsonar.java.binaries=. '''
                }
            }
        }
        
        stage('Quality Gate Check') {
            steps {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-token'
            }
        }    
        
        stage('Build') {
            agent { docker 'maven:3.8.6-openjdk-8' }
            steps {
                git branch: 'main', url: 'https://github.com/waldra/shopping-cart.git'
                sh 'mvn clean package -DskipTests=true'
            }
        }
    }
}

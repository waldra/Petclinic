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
                    sh ''' ${SCANNER_HOME}/bin/sonar-scanner
                          -Dsonar.java.binaries=. \
                          -Dsonar.projectKey=shopping-card \
                          -Dsonar.projectName=shopping-card '''
                }
            }
        }
        
        stage('Quality Gate Check') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-token'
                }
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

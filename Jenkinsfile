pipeline {
    agent any
    
    tools {
        jdk 'JAVA_HOME'
        maven 'Maven3'
    }
    
    // Déclenche automatiquement toutes les 2 minutes s'il y a un nouveau commit
    triggers { pollSCM('H/2 * * * *') }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/smailch/StudentsManagement-DevOps.git'
            }
        }
        
        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Docker Build') {
            steps {
                sh 'docker build -t smailch/students-management:latest .'
                sh 'docker tag smailch/students-management:latest smailch/students-management:${BUILD_NUMBER}'
            }
        }
        
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                 usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push smailch/students-management:latest'
                    sh 'docker push smailch/students-management:${BUILD_NUMBER}'
                }
            }
        }
    }
    
    post {
        success { echo 'Pipeline CI/CD complète terminée – Image publiée sur Docker Hub !' }
        failure { echo 'Échec de la pipeline' }
    }
}
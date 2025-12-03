pipeline {
    agent any

    stages {
        stage('Checkout') {                     
                checkout scm
            }
        }

        stage('Maven Build') {               
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {                 
            steps {
                sh 'docker build -t missaouimourad/student-management:latest .'
            }
        }

        stage('Docker Push') {                 
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push missaouimourad/student-management:latest'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline CI/CD complète réussie ! Image publiée sur https://hub.docker.com/r/missaouimourad/student-management'
        }
        failure {
            echo 'Pipeline échouée – vérifiez les logs'
        }
    }
}
pipeline {
    agent any

    // Déclenche automatiquement toutes les 2 minutes s'il y a un nouveau commit
    triggers { pollSCM('H/2 * * * *') }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mouradmissa/DevOps.git'
            }
        }

        stage('Maven Build') {
            steps {
                // Utilise le Maven déjà installé sur la VM
                sh '/usr/share/maven/bin/mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t mouradmissa/student-management:latest .'
                sh 'docker tag mouradmissa/student-management:latest mouradmissa/student-management:${BUILD_NUMBER}'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                 usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push mouradmissa/student-management:latest'
                    sh 'docker push mouradmissa/student-management:${BUILD_NUMBER}'
                }
            }
        }
    }

    post {
        success { echo 'Pipeline CI/CD complète terminée – Image publiée sur Docker Hub !' }
        failure { echo 'Échec de la pipeline' }
    }
}

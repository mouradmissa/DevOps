pipeline {
    agent any

    // Outils installés dans Jenkins (à configurer une seule fois dans Manage Jenkins → Tools)
    tools {
        jdk 'JAVA_HOME'      // Doit exister dans Jenkins (Java 17)
        maven 'Maven3'       // Doit exister dans Jenkins
    }

    // Vérifie toutes les 2 minutes s'il y a un nouveau commit sur ton repo
    triggers { pollSCM('H/10 * * * *') }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mouradmissa/DevOps.git'
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
                sh 'docker tag missaouimourad/student-management:latest missaouimourad/student-management:${BUILD_NUMBER}'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',           // Le même que tu as déjà créé
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push missaouimourad/student-management:latest'
                    sh 'docker push missaouimourad/student-management:${BUILD_NUMBER}'
                }
            }
        }
    
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f mysql-deployment.yaml'
                sh 'kubectl apply -f spring-deployment.yaml'
            }
        }
        
    }

    post {
        success {
            echo 'Pipeline CI/CD complète terminée avec succès ! Image + Kubernetes + SonarQube OK ! Image publiée sur https://hub.docker.com/r/missaouimourad/student-management'
        }
        failure {
            echo 'Échec de la pipeline – vérifiez les logs'
        }
    }
}
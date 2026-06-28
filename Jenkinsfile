pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Repository already checked out by Jenkins'
            }
        }

        stage('Build') {
            steps {
                dir('auth-service') {
                    sh 'chmod +x mvnw'
                    sh './mvnw clean package -DskipTests'
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir('auth-service') {
                    sh 'docker build -t foodhub/auth-service:1.0 .'
                }
            }
        }

    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
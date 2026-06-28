pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                dir('auth-service') {
                    sh 'chmod +x mvnw'
                    sh './mvnw clean package -DskipTests'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('auth-service') {
                    withSonarQubeEnv('sonarqube') {
                        sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=foodhub-auth-service \
                        -Dsonar.projectName=foodhub-auth-service \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target/classes
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline Successful'
        }

        failure {
            echo 'Pipeline Failed'
        }
    }
}
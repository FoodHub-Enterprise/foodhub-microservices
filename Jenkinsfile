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
                sh '''
                ./mvnw sonar:sonar \
                  -Dsonar.projectKey=foodhub-auth-service
                '''
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
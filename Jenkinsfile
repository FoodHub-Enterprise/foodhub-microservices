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
       stage('Quality Gate') {

    steps {

        timeout(time: 5, unit: 'MINUTES') {

            waitForQualityGate abortPipeline: true

        }

    }
  stage('Docker Build') {
    steps {
        dir('auth-service') {
            sh '''
                docker build \
                  -t foodhub-auth-service:${BUILD_NUMBER} \
                  .
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

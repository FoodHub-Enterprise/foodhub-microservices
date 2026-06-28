pipeline {
    agent any

    tools {
        jdk 'jdk21'
    }

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
                $SONAR_SCANNER_HOME/bin/sonar-scanner \
                  -Dsonar.projectKey=foodhub-auth-service \
                  -Dsonar.projectName=foodhub-auth-service \
                  -Dsonar.sources=src \
                  -Dsonar.java.binaries=target/classes
                '''
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
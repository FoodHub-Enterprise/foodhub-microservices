pipeline {
    agent any

    environment {
     AWS_REGION = 'ap-south-1'
     ECR_REPOSITORY = 'foodhub-auth-service'
     AWS_ACCOUNT_ID = '908340074181'
     IMAGE_TAG = "${BUILD_NUMBER}"
     ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
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
              -t ${ECR_REPOSITORY}:${IMAGE_TAG} \
              .
            '''
        }
    }
}
stage('Verify AWS CLI') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'aws-ecr',
            usernameVariable: 'AWS_ACCESS_KEY_ID',
            passwordVariable: 'AWS_SECRET_ACCESS_KEY'
        )]) {
            sh '''
            aws sts get-caller-identity
            '''
        }
    }
}
stage('Login to Amazon ECR') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'aws-ecr',
            usernameVariable: 'AWS_ACCESS_KEY_ID',
            passwordVariable: 'AWS_SECRET_ACCESS_KEY'
        )]) {

            sh '''
            aws ecr get-login-password \
              --region ${AWS_REGION} | \
            docker login \
              --username AWS \
              --password-stdin \
              ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
            '''
        }
    }
}
stage('Push Image to Amazon ECR') {
    steps {

        sh '''
        docker tag \
        ${ECR_REPOSITORY}:${IMAGE_TAG} \
        ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}

        docker push \
        ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}
        '''
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


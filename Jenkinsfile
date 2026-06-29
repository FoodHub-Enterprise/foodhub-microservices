pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '908340074181'
        ECR_REPOSITORY = 'foodhub-auth-service'
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

        stage('Trivy Image Scan') {
            steps {
                sh '''
                trivy image \
                  --severity HIGH,CRITICAL \
                  --exit-code 0 \
                  ${ECR_REPOSITORY}:${IMAGE_TAG}
                '''
            }
        }

        stage('Verify AWS Credentials') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'aws-ecr',
                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                        passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                    )
                ]) {
                    sh '''
                    aws sts get-caller-identity
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
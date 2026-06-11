pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = '910617066292.dkr.ecr.us-east-1.amazonaws.com/nexusdeploy'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'

                    withSonarQubeEnv('SonarQube') {
                        sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=NexusDeploy \
                        -Dsonar.projectName=NexusDeploy \
                        -Dsonar.sources=.
                        """
                    }
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                sh 'trivy fs .'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t nexusdeploy:${BUILD_NUMBER} .
                """
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region us-east-1 | \
                docker login --username AWS --password-stdin 910617066292.dkr.ecr.us-east-1.amazonaws.com
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                docker tag nexusdeploy:${BUILD_NUMBER} ${ECR_REPO}:${BUILD_NUMBER}
                docker push ${ECR_REPO}:${BUILD_NUMBER}
                '''
            }
        }
    }
}

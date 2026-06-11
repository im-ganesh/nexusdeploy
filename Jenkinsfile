pipeline {
    agent any

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
                sh 'docker build -t nexusdeploy:v1 .'
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh '''
                aws ecs update-service \
                --cluster silent-hamster-zdpb94 \
                --service nexusdeploy-task-service-lxmq52vu \
                --force-new-deployment
                '''
            }
        }

    }
}

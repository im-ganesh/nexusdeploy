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

        stage('Run Container') {
            steps {
                sh '''
                docker rm -f nexusdeploy-container || true
                docker run -d --name nexusdeploy-container -p 80:80 nexusdeploy:v1
                '''
            }
        }
    }
}

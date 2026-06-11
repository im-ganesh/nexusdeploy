pipeline {
    agent any

    tools {
        sonarQube 'SonarScanner'
    }

    stages {

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=NexusDeploy \
                    -Dsonar.projectName=NexusDeploy \
                    -Dsonar.sources=.
                    '''
                }
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

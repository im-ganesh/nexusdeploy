pipeline {
agent any

```
environment {
    AWS_REGION = 'us-east-1'
    ECR_REPO = '910617066292.dkr.ecr.us-east-1.amazonaws.com/nexusdeploy'
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
            sh 'docker build -t nexusdeploy:v1 .'
        }
    }

    stage('Login to ECR') {
        steps {
            sh '''
            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 910617066292.dkr.ecr.us-east-1.amazonaws.com
            '''
        }
    }

    stage('Push Image to ECR') {
        steps {
            sh '''
            docker tag nexusdeploy:v1 ${ECR_REPO}:v1
            docker push ${ECR_REPO}:v1
            '''
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
```

}

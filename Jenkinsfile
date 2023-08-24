pipeline {
    agent any
    environment {
        ECR_REGISTRY="https://xxxxxx.dkr.ecr.ap-south-1.amazonaws.com"
        APP_REPO_NAME="xxxxxxxx.dkr.ecr.ap-south-1.amazonaws.com/app-registry"
        AWS_REGION="ap-south-1"
    }
    stages{
        stage('checkout'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/RAM28EC/javascript-deploy-public.git']])

            }
        }
        stage('build and push image'){
            steps{
                script{
                    sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}"
                    sh "docker build -t app-registry ."
                    sh "docker tag app-registry:latest ${APP_REPO_NAME}:latest"
                    sh "docker push ${APP_REPO_NAME}:latest"
                }
            }
        }
        stage('Deploy') {
            steps {
                script{
                    sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}"
                    sh "docker pull ${APP_REPO_NAME}:latest"
                    sh 'docker rm -f app-do | echo "there is no docker container named app-do "'
                    sh "docker run -d --name app-do 80:3000 ${APP_REPO_NAME}:latest"
                }
            } 
        }
    }
    post {
        always {
            echo 'Deleting all local images'
            sh 'docker image prune -af'
        }
    }

}


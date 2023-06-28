pipeline {
    environment {
        AWS_ACCOUNT_ID="392292519055"
        AWS_DEFAULT_REGION="us-east-1" 
        IMAGE_REPO_NAME="test123"
        IMAGE_TAG="latest" 
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    } 
    agent any
    stages {


        stage('Checkout') {
            steps {
                script{
                    git credentialsId: '22d727d6-8992-478d-b6c5-fafbb1db63e8', url: 'https://github.com/magarGanga/my-source.git'  

                }
            }
        }
        
        stage('Logging into AWS ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                }
            } 
            
        }

        stage('Building image') {
            steps{
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${BUILD_ID}"
                }
            }
        }

        stage('Publish to ECR') {
            steps{
                script {
                   sh "docker tag ${IMAGE_REPO_NAME}:${BUILD_ID} ${REPOSITORY_URI}:${BUILD_ID}"
                   sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_ID}"
                }
            }
        }

        stage('Trigger ManifestUpdate') {
            steps {
                echo "Triggering UpdatemanifestJob"
                build job: 'UpdatemanifestJob-ArgoCD', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_ID)]
            }           
        }




           
    }
}

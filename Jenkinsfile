pipeline {
    agent any
    environment {
        IMAGE_NAME = "${env.GIT_BRANCH.toLowerCase()}:v${env.BUILD_ID}"
        PORT = 52020
        AWS_REGION = 'us-east-1' 
        ECR_REGISTRY = 'public.ecr.aws/e9i1b6d1'
    }
    stages {
        stage('Build') {
            steps {
                script {
                    docker.build("${env.IMAGE_NAME}", "--no-cache ./app")
                }
            }
        }       
        stage('Verify images') {
            steps {
                script {
                    def exitCode = sh(script: "docker inspect ${env.IMAGE_NAME} >/dev/null 2>&1", returnStatus: true)
                    if (exitCode != 0 ) {
                        error "One or more builds failed"
                    }
                }
            }
        }
        // stage('Testing'){
        //     steps {
        //         sh "docker run -p ${env.PORT}:5000  ${env.IMAGE_NAME}  &"
        //         script {
        //             def response = null
        //             retry(3) {
        //                 sleep 10
        //                 response = httpRequest "http://localhost:${env.PORT}"
        //                 if ((response == null) || (response.status != 200)) {
        //                     error "Failed to get a successful response"
        //                 }
        //             }
        //         }
        //     }
        // }
        stage('Publish image'){
            steps{
                 withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'jenkins-aws-cli']]) {
                    sh "aws ecr-public get-login-password --region ${env.AWS_REGION} | docker login --username AWS --password-stdin ${env.ECR_REGISTRY}"
                    sh "docker tag ${env.IMAGE_NAME} ${env.ECR_REGISTRY}/hothaifazoubi"
                    sh "docker push ${env.ECR_REGISTRY}:latest"
                }
            }
        } 
    }
    post {
        cleanup {
            sh "docker rmi -f ${env.ECR_REGISTRY}/hothaifazoubi"
            cleanWs()
        }
    }
    
}
    

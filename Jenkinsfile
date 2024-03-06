pipeline {
    agent any

    environment {
        registry = "176422384401.dkr.ecr.us-west-2.amazonaws.com/my-docker-repo"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/systemops-de/docker-spring-boot']])
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage ("Build Image") {
            steps {
                script {
                    dockerImage = docker.build registry 
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 176422384401.dkr.ecr.us-west-2.amazonaws.com"
                    sh "docker push 176422384401.dkr.ecr.us-west-2.amazonaws.com/my-docker-repo:$BUILD_NUMBER"
                    
                }
            }
        }
        
                        
        stage ("Helm Deploy") {
            steps {
                    sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
    }
}

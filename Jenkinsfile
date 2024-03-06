pipeline {
    agent any

    environment {
        registry = "176422384401.dkr.ecr.us-west-2.amazonaws.com/my-docker-repo"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage ("Build Docker Image") {
            steps {
                script {
                    dockerImage = docker.build("$registry:$BUILD_NUMBER")
                }
            }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 176422384401.dkr.ecr.us-west-2.amazonaws.com"
                    sh "docker push ${registry}:${BUILD_NUMBER}"
                }
            }
        }
        
        stage ("Helm Deploy") {
            steps {
                script {
                    withKubeConfig([credentialsId: 'kubelogin']) {
                        sh ("helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER")
                    }
                }
            }
        }    
    }
}

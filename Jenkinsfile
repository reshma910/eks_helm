pipeline {
    agent any

    environment {
        registry = "583144934302.dkr.ecr.us-east-1.amazonaws.com/eks_helm"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/reshma910/eks_helm.git']])
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
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 583144934302.dkr.ecr.us-east-1.amazonaws.com"
                    sh "docker push 583144934302.dkr.ecr.us-east-1.amazonaws.com/eks_helm:$BUILD_NUMBER"
                    
                }
            }
        }
        
        stage ("Helm package") {
            steps {
                     script {
                     sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
                }
            }
        
    }
}
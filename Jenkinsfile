pipeline {
    agent any
    environment {
        registry = 'public.ecr.aws/h5g6h5l1/cluster'
    }
    tools {
        maven 'Maven'
    }
    stages {
        stage('Hello') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/joelyoung2020/Jenkins-ECR--EKS.git']]])
            }
        }
        stage('build mvn') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('building image') {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        stage('pushing to ecr'){
            steps {
                sh 'aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/h5g6h5l1'
                sh 'docker push public.ecr.aws/h5g6h5l1/cluster:latest'
            }
        }
        stage('deploying on k8s') {
            steps {
               withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', serverUrl: '') {
                     sh 'kubectl apply -f eks-deploy-k8s.yaml'
                }
            }
        }
    }
}

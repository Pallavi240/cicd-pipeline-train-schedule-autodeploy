pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "pallu24/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                bat 'npm install'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def app = docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    def app = docker.build(DOCKER_IMAGE_NAME)
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('CanaryDeploy') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    bat 'kubectl apply -f train-schedule-kube-canary.yml'
                }
            }
        }
        stage('DeployToProduction') {
            steps {
                input 'Deploy to Production?'
                milestone(1)
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    bat 'kubectl apply -f train-schedule-kube-canary.yml'
                    bat 'kubectl apply -f train-schedule-kube.yml'
                }
            }
        }
    }
}
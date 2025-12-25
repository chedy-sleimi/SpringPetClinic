pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        DOCKER_IMAGE = 'chedysleimi/springpetclinic'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/chedy-sleimi/SpringPetClinic.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS')]) {
                        bat "echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin"
                        bat "docker push ${DOCKER_IMAGE}:latest"
                    }
                }
            }
        }

       stage('Deploy to Kubernetes') {
                   steps {
                       script {
                           bat 'minikube kubectl -- apply -f deployment.yaml'
                           bat 'minikube kubectl -- apply -f service.yaml'
                           bat 'minikube kubectl -- rollout status deployment/springpetclinic-deployment'
                       }
                   }
               }
           }

           post {
               success {
                   echo '========================================='
                   echo '🎉 Pipeline completed successfully!'
                   echo '========================================='
                   bat 'minikube kubectl -- get pods'
                   bat 'minikube kubectl -- get svc'
                   bat 'minikube service springpetclinic-service --url'
                   echo 'Access the app using the URL above!'
               }
               failure {
                   echo '❌ Build failed!  Check the console output.'
               }
           }
       }

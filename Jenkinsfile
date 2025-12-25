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

      post {
              success {
                  echo '========================================='
                  echo '🎉 CI Pipeline completed successfully!'
                  echo '========================================='
                  echo 'Docker image pushed to:  chedysleimi/springpetclinic: latest'
                  echo ''
                  echo 'To deploy to Kubernetes, run these commands:'
                  echo 'kubectl apply -f deployment.yaml'
                  echo 'kubectl apply -f service.yaml'
                  echo 'minikube service springpetclinic-service --url'
              }
              failure {
                  echo '❌ Build failed!  Check the console output.'
              }
          }
      }

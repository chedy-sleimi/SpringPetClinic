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


    }

    post {
        success {
            echo '🎉 Pipeline completed successfully!'
            echo 'Get Minikube IP: minikube ip'
            echo 'Access app at: http://<minikube-ip>: 30080'
        }
        failure {
            emailext(
                to: 'c. sleimi23069@pi. tn',
                subject: "Build Failed: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body:  "Job: ${env.JOB_NAME}\nBuild: ${env.BUILD_NUMBER}\nURL: ${env.BUILD_URL}"
            )
        }
    }
}

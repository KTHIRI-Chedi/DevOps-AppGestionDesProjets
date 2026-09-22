pipeline {
    agent any

    environment {
        DOCKER_USER = 'your-dockerhub-username'
        IMAGE_BACKEND = "${DOCKER_USER}/project-backend:${BUILD_NUMBER}"
        IMAGE_FRONTEND = "${DOCKER_USER}/project-frontend:${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Alaa-Rami/DevOps-AppGestionDesProjets.git'
            }
        }

        stage('Build Application') {
            steps {
                dir('backend') {
                    sh './mvnw clean package -DskipTests'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_BACKEND} ./backend"
                    sh "docker build -t ${IMAGE_FRONTEND} ./frontend"
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USER_VAR', passwordVariable: 'DOCKER_PASS_VAR')]) {
                        sh "echo \$DOCKER_PASS_VAR | docker login -u \$DOCKER_USER_VAR --password-stdin"
                        sh "docker push ${IMAGE_BACKEND}"
                        sh "docker push ${IMAGE_FRONTEND}"
                    }
                }
            }
        }

        stage('Deploy Stack via Docker Compose') {
            steps {
                sh 'docker compose down'
                sh 'docker compose up -d --build'
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}

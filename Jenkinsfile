pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub_credentials')
        DOCKER_IMAGE = 'lllyyyqqqq/teedy'  
        DOCKER_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Clean') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test -Dmaven.test.failure.ignore=true -Dtest="!com.sismics.util.format.TestPdfFormatHandler"'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push('latest')
                    }
                }
            }
        }

        stage('Run Three Containers') {
            steps {
                script {
                    // 清理旧容器
                    sh 'docker stop teedy-8082 || true'
                    sh 'docker rm teedy-8082 || true'
                    sh 'docker stop teedy-8083 || true'
                    sh 'docker rm teedy-8083 || true'
                    sh 'docker stop teedy-8084 || true'
                    sh 'docker rm teedy-8084 || true'

                    // 运行三个容器
                    sh "docker run -d -p 8082:8080 --name teedy-8082 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker run -d -p 8083:8080 --name teedy-8083 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker run -d -p 8084:8080 --name teedy-8084 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            archiveArtifacts artifacts: '**/target/site/**', allowEmptyArchive: true
            cleanWs()
        }
    }
}
pipeline {
    agent any

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
                sh 'mvn test -Dmaven.test.failure.ignore=true'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('PMD') {
            steps {
                sh 'mvn pmd:pmd || true'
            }
        }
        stage('JaCoCo') {
            steps {
                sh 'mvn jacoco:report || true'
            }
        }
        stage('Site') {
            steps {
                sh 'mvn site || true'
            }
        }
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
                archiveArtifacts artifacts: '**/target/site/**', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
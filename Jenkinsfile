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
                sh 'mvn test -Dmaven.test.failure.ignore=true -Dtest="!com.sismics.util.format.TestPdfFormatHandler"'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('PMD') {
            steps {
                sh 'mvn pmd:pmd'
            }
        }
        
        stage('JaCoCo') {
            steps {
                sh 'mvn jacoco:report'
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }
        
        stage('Site') {
            steps {
                sh 'mvn site'
            }
        }

        stage('Stage Site') {
            steps {
                sh 'mvn site:stage'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/staging/**', fingerprint: true

            archiveArtifacts artifacts: '**/target/site/**/*.*', fingerprint: true
            archiveArtifacts artifacts: '**/target/**/*.jar', fingerprint: true
            archiveArtifacts artifacts: '**/target/**/*.war', fingerprint: true

            junit '**/target/surefire-reports/*.xml'
            // cleanWs()  
        }
    }
}
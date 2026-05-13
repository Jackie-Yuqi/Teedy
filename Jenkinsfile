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
    }

    post {
        always {
            // 归档所有 war 包
            archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
            // 归档所有 jar 包
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            // 归档 docs-core 的 site 报告
            archiveArtifacts artifacts: 'docs-core/target/site/**', allowEmptyArchive: true
            // 归档 docs-web 的 site 报告
            archiveArtifacts artifacts: 'docs-web/target/site/**', allowEmptyArchive: true
            // 归档 docs-web-common 的 site 报告
            archiveArtifacts artifacts: 'docs-web-common/target/site/**', allowEmptyArchive: true
            // 归档根目录的 site（如果有）
            archiveArtifacts artifacts: 'target/site/**', allowEmptyArchive: true
            // 清理工作空间
            cleanWs()
        }
    }
}
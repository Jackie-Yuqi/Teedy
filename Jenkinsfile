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
                    // 收集测试报告，Jenkins 会解析并展示
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
        stage('Site') {
            steps {
                sh 'mvn site'
            }
        }
        stage('Archive Artifacts') {
            steps {
                // 归档 war 包（如果有）
                archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
                // 归档整个站点文档（方便查看）
                archiveArtifacts artifacts: '**/target/site/**', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            // 清理工作空间（可选）
            cleanWs()
        }
    }
}


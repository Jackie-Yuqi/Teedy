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

        stage('Assemble Site') {
            steps {
                sh '''#!/bin/bash
                    ASSEMBLY_DIR="${WORKSPACE}/assembled-site"
                    rm -rf "$ASSEMBLY_DIR"
                    mkdir -p "$ASSEMBLY_DIR"

                    # 定义模块列表（父模块用 "." 表示）
                    MODULES=("." "docs-core" "docs-web-common" "docs-web")

                    # 复制各模块的 target/site 到统一目录
                    for mod in "${MODULES[@]}"; do
                        SRC="${WORKSPACE}/${mod}/target/site"
                        if [ -d "$SRC" ]; then
                            if [ "$mod" = "." ]; then
                                # 父模块直接放在根层级
                                cp -r "$SRC"/* "$ASSEMBLY_DIR/"
                            else
                                # 子模块放在同名子目录下
                                mkdir -p "$ASSEMBLY_DIR/${mod}"
                                cp -r "$SRC"/* "$ASSEMBLY_DIR/${mod}/"
                            fi
                        else
                            echo "Warning: $SRC not found, skipping ${mod}"
                        fi
                    done

                    find "$ASSEMBLY_DIR" -name "*.html" -exec sed -i 's|href="../docs-core/|href="docs-core/|g' {} \\;
                    find "$ASSEMBLY_DIR" -name "*.html" -exec sed -i 's|href="../docs-web-common/|href="docs-web-common/|g' {} \\;
                    find "$ASSEMBLY_DIR" -name "*.html" -exec sed -i 's|href="../docs-web/|href="docs-web/|g' {} \\;

                    echo "Site assembled to $ASSEMBLY_DIR"
                '''
            }
        }
    }

    post {
        always {

            archiveArtifacts artifacts: 'assembled-site/**', fingerprint: true

            archiveArtifacts artifacts: '**/target/site/**/*.*', fingerprint: true
            archiveArtifacts artifacts: '**/target/**/*.jar', fingerprint: true
            archiveArtifacts artifacts: '**/target/**/*.war', fingerprint: true

            junit '**/target/surefire-reports/*.xml'
            // cleanWs()
        }
    }
}
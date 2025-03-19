pipeline {
    agent any
    
    environment {
        NODE_VERSION = '18'
        ADMIN_PATH = 'admin'
        USER_PATH = 'user'
    }
    
    stages {
        stage('Preparation') {
            steps {
                // Sử dụng NodeJS đã cài trong Jenkins
                tool name: 'NodeJS', version: "${env.NODE_VERSION}"
                sh 'node --version'
                sh 'npm --version'
            }
        }
        
        stage('Checkout') {
            steps {
                // Đảm bảo checkout code mới nhất
                checkout scm
                sh 'ls -la' // Kiểm tra thư mục có admin và user không
            }
        }
        
        stage('Install Dependencies') {
            parallel {
                stage('Admin Frontend') {
                    steps {
                        dir("${env.ADMIN_PATH}") {
                            sh 'npm install'
                        }
                    }
                }
                stage('User Frontend') {
                    steps {
                        dir("${env.USER_PATH}") {
                            sh 'npm install'
                        }
                    }
                }
            }
        }
        
        stage('Build') {
            parallel {
                stage('Build Admin') {
                    steps {
                        dir("${env.ADMIN_PATH}") {
                            sh 'npm run build'
                        }
                    }
                }
                stage('Build User') {
                    steps {
                        dir("${env.USER_PATH}") {
                            sh 'npm run build'
                        }
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying applications...'
                dir("${env.ADMIN_PATH}/dist") {
                    echo 'Deploying admin frontend...'
                    // Ví dụ: sh 'aws s3 sync . s3://your-admin-bucket --region your-region'
                }
                dir("${env.USER_PATH}/dist") {
                    echo 'Deploying user frontend...'
                    // Ví dụ: sh 'aws s3 sync . s3://your-user-bucket --region your-region'
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Build và deployment hoàn tất thành công!'
        }
        failure {
            echo '❌ Build thất bại! Kiểm tra logs để biết chi tiết.'
        }
        always {
            cleanWs()
        }
    }
}

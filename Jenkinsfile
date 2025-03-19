pipeline {
    agent any
    
    tools {
        nodejs 'Node16' 
    }
    
    environment {
        ADMIN_PATH = 'admin'
        USER_PATH = 'user'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Đang checkout source code........'
                checkout scm
                echo 'Kiểm tra cấu trúc thư mục........'
                sh 'ls -la'
                sh 'find . -name package.json'
            }
        }
        
        stage('Install Dependencies') {
            parallel {
                stage('Admin Frontend') {
                    steps {
                        dir("${env.ADMIN_PATH}") {
                            echo 'Cài đặt dependencies cho admin...'
                            sh 'ls -la'
                            sh 'npm install'
                        }
                    }
                }
                stage('User Frontend') {
                    steps {
                        dir("${env.USER_PATH}") {
                            echo 'Cài đặt dependencies cho user...'
                            sh 'ls -la'
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
                            echo 'Building admin frontend...'
                            sh 'npm run build'
                        }
                    }
                }
                stage('Build User') {
                    steps {
                        dir("${env.USER_PATH}") {
                            echo 'Building user frontend...'
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
                    // Ví dụ: sh 'aws s3 sync . s3://admin-bucket'
                }
                dir("${env.USER_PATH}/dist") {
                    echo 'Deploying user frontend...'
                    // Ví dụ: sh 'aws s3 sync . s3://user-bucket'
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Build hoàn tất thành công!'
        }
        failure {
            echo '❌ Build thất bại! Kiểm tra logs để biết chi tiết.'
        }
        always {
            cleanWs()
        }
    }
}

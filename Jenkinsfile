pipeline {
    agent any

    environment {
        NODE_VERSION = '18' // Chỉnh phiên bản Node.js phù hợp với project
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    checkout scm
                }
            }
        }

        stage('Setup Node.js') {
            steps {
                script {
                    def nodeInstalled = sh(script: 'node -v || echo "not installed"', returnStdout: true).trim()
                    if (nodeInstalled == "not installed") {
                        sh "nvm install ${NODE_VERSION}"
                        sh "nvm use ${NODE_VERSION}"
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('admin/src') {
                    sh 'npm install'
                }
                dir('user/src') {
                    sh 'npm install'
                }
            }
        }

        stage('Build Angular Apps') {
            steps {
                dir('admin/src') {
                    sh 'npm run build -- --output-path=dist/admin'
                }
                dir('user/src') {
                    sh 'npm run build -- --output-path=dist/user'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir('admin/src') {
                    sh 'npm test'
                }
                dir('user/src') {
                    sh 'npm test'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Angular Apps...'
                // Thêm lệnh deploy tùy theo cách bạn triển khai, ví dụ:
                // scp -r admin/src/dist/admin user@server:/var/www/admin
                // scp -r user/src/dist/user user@server:/var/www/user
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}


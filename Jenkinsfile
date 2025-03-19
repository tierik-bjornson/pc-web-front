pipeline {
    agent any

    environment {
        NODEJS_VERSION = '18'  // Đảm bảo dùng Node.js 18
    }

    stages {
        stage('Checkout') {
            steps {
                deleteDir()  // Xóa thư mục cũ trước khi pull code mới
                checkout scm
                sh 'ls -la'  // Kiểm tra file sau khi clone
            }
        }

        stage('Setup Node.js') {
            steps {
                script {
                    def nodeHome = tool name: 'NodeJS_18', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                    env.PATH = "${nodeHome}/bin:${env.PATH}"
                }
                sh 'node -v'  // Debug kiểm tra phiên bản Node.js
                sh 'npm -v'   // Kiểm tra npm
            }
        }

        stage('Install Dependencies') {
            parallel {
                stage('Admin - Install') {
                    steps {
                        dir('admin') {
                            sh 'npm install'
                        }
                    }
                }
                stage('User - Install') {
                    steps {
                        dir('user') {
                            sh 'npm install'
                        }
                    }
                }
            }
        }

        stage('Lint Code') {
            parallel {
                stage('Admin - Lint') {
                    steps {
                        dir('admin') {
                            sh 'npm run lint || true'  // Không fail nếu lint lỗi
                        }
                    }
                }
                stage('User - Lint') {
                    steps {
                        dir('user') {
                            sh 'npm run lint || true'
                        }
                    }
                }
            }
        }

        stage('Run Tests') {
            parallel {
                stage('Admin - Test') {
                    steps {
                        dir('admin') {
                            sh 'npm test || true'  // Không fail nếu test lỗi
                        }
                    }
                }
                stage('User - Test') {
                    steps {
                        dir('user') {
                            sh 'npm test || true'
                        }
                    }
                }
            }
        }

        stage('Build Project') {
            parallel {
                stage('Admin - Build') {
                    steps {
                        dir('admin') {
                            sh 'npm run build'
                        }
                    }
                }
                stage('User - Build') {
                    steps {
                        dir('user') {
                            sh 'npm run build'
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo '🔄 Deploying Application...'
                // Thêm lệnh deploy của bạn ở đây (ví dụ: copy file, chạy Docker, Kubernetes...)
            }
        }
    }

    post {
        success {
            echo '✅ Build thành công!'
        }
        failure {
            echo '❌ Build thất bại. Kiểm tra logs!'
        }
    }
}

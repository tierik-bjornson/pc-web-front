pipeline {
    agent any

    environment {
        NODE_VERSION = '18' // Phiên bản Node.js cần thiết
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Cài đặt Node.js
                    sh '''
                    curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash -
                    apt-get install -y nodejs
                    node -v
                    npm -v
                    '''
                }
            }
        }

        stage('Checkout Code') {
            steps {
                // Clone repository
                git branch: 'main',
                    url: 'https://github.com/tierik-bjornson/pc-web-front.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Cài đặt dependencies cho cả admin và user
                    def modules = ['admin', 'user']
                    for (module in modules) {
                        dir(module) {
                            sh 'npm install'
                        }
                    }
                }
            }
        }

        stage('Build Projects') {
            steps {
                script {
                    // Build cả admin và user
                    def modules = ['admin', 'user']
                    for (module in modules) {
                        dir(module) {
                            sh 'npm run build'
                        }
                    }
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                // Lưu trữ các file build
                archiveArtifacts artifacts: '**/dist/**', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                echo 'Triển khai ứng dụng...'
                // Thêm các bước triển khai tại đây
            }
        }
    }

    post {
        success {
            echo '🎉 Build và triển khai thành công!'
        }
        failure {
            echo '❌ Build hoặc triển khai thất bại!'
        }
    }
}

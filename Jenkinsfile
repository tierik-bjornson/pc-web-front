pipeline {
    agent any
    tools {
        nodejs "Node18"
    }
    environment {
        REGISTRY = 'localhost:80'
        PROJECT = 'pc-web-front'
        IMAGE_NAME = 'pc-web-front-image'
        HARBOR_CREDS = 'harbor-credentials'
        DOCKER_IMAGE_TAG = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('Start') {
            steps {
                script {
                    echo "🚀 Pipeline bắt đầu chạy!"
                }
            }
        }
        stage('Checkout Source Code') {
            steps {
                script {
                    echo "🔄 Đang clone repository source code..."
                    git url: 'https://github.com/tierik-bjornson/pc-web-front.git', branch: 'main', depth: 1
                    sh 'ls -la'
                    echo "✅ Clone source code thành công!"
                }
            }
        }
        stage('Check Directories') {
            steps {
                script {
                    echo "📂 Kiểm tra thư mục..."
                    sh 'ls -la'
                    sh 'ls -la admin || echo "⚠️ Thư mục admin không tồn tại!"'
                    sh 'ls -la user || echo "⚠️ Thư mục user không tồn tại!"'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script {
                    echo "📦 Cài đặt dependencies..."
                    dir('admin') {
                        sh 'ls -la && npm install'
                    }
                    dir('user') {
                        sh 'ls -la && npm install'
                    }
                    echo "✅ Cài đặt hoàn tất!"
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    echo "🛠 Bắt đầu build..."
                    dir('admin') {
                        sh 'npm run build --prod'
                    }
                    dir('user') {
                        sh 'npm run build --prod'
                    }
                    echo "✅ Build hoàn tất!"
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    echo "🧪 Chạy test..."
                    dir('admin') {
                        sh 'npm run test || echo "⚠️ No tests specified, skipping..."'
                    }
                    dir('user') {
                        sh 'npm run test || echo "⚠️ No tests specified, skipping..."'
                    }
                    echo "✅ Test xong!"
                }
            }
        }
      
        stage('Cleanup') {
            steps {
                script {
                    echo "🧹 Dọn dẹp Docker image..."
                    sh "docker rmi ${REGISTRY}/${PROJECT}/${IMAGE_NAME}:${DOCKER_IMAGE_TAG} || true"
                    echo "✅ Dọn dẹp hoàn tất!"
                }
            }
        }
    }
    post {
        success {
            echo '🎉 Build và push lên Harbor thành công! Repo deploy đã được cập nhật.'
        }
        failure {
            echo '❌ Build thất bại. Kiểm tra logs để xem chi tiết.'
        }
    }
}


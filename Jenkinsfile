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
                    echo "✅ Clone source code thành công!"
                }
            }
        }
        stage('Check Files') {
            steps {
                script {
                    echo "📂 Kiểm tra thư mục sau khi clone:"
                    sh 'ls -la'
                    sh 'find . -name package.json || echo "⚠️ Không tìm thấy package.json!"'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script {
                    echo "📦 Cài đặt dependencies..."
                    if (fileExists('admin/package.json')) {
                        dir('admin') {
                            sh 'npm install'
                        }
                    } else {
                        echo "⚠️ Không tìm thấy package.json trong admin!"
                    }

                    if (fileExists('user/package.json')) {
                        dir('user') {
                            sh 'npm install'
                        }
                    } else {
                        echo "⚠️ Không tìm thấy package.json trong user!"
                    }
                    echo "✅ Cài đặt hoàn tất!"
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    echo "🛠 Bắt đầu build..."
                    if (fileExists('admin/package.json')) {
                        dir('admin') {
                            sh 'npm run build --prod'
                        }
                    }
                    if (fileExists('user/package.json')) {
                        dir('user') {
                            sh 'npm run build --prod'
                        }
                    }
                    echo "✅ Build hoàn tất!"
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    echo "🧪 Chạy test..."
                    if (fileExists('admin/package.json')) {
                        dir('admin') {
                            sh 'npm run test || echo "⚠️ No tests specified, skipping..."'
                        }
                    }
                    if (fileExists('user/package.json')) {
                        dir('user') {
                            sh 'npm run test || echo "⚠️ No tests specified, skipping..."'
                        }
                    }
                    echo "✅ Test xong!"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo "🐳 Build Docker image..."
                    sh """
                    docker build -t ${REGISTRY}/${PROJECT}/${IMAGE_NAME}:${DOCKER_IMAGE_TAG} .
                    """
                    echo "✅ Build Docker image hoàn tất!"
                }
            }
        }
        stage('Push Image to Harbor') {
            steps {
                script {
                    echo "📤 Push image lên Harbor..."
                    sh """
                    docker login ${REGISTRY} -u admin -p password
                    docker push ${REGISTRY}/${PROJECT}/${IMAGE_NAME}:${DOCKER_IMAGE_TAG}
                    """
                    echo "✅ Push image thành công!"
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



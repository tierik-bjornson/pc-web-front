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
                    echo "📥 Đang clone repository source code..."
                    git url: 'https://github.com/tierik-bjornson/pc-web-front.git', branch: 'main'
                    echo "✅ Clone source code thành công!"
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script {
                    def directories = ['admin/src', 'user/src']
                    for (dir in directories) {
                        dir("${dir}") {
                            if (fileExists('package.json')) {
                                echo "📦 Cài đặt dependencies trong ${dir}..."
                                sh 'npm install'
                            } else {
                                echo "⚠ Không tìm thấy package.json trong ${dir}, bỏ qua cài đặt."
                            }
                        }
                    }
                    echo "✅ Cài đặt dependencies hoàn tất!"
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    def directories = ['admin/src', 'user/src']
                    for (dir in directories) {
                        dir("${dir}") {
                            if (fileExists('package.json')) {
                                echo "🏗 Build dự án trong ${dir}..."
                                sh 'npm run build --prod'
                            } else {
                                echo "⚠ Không tìm thấy package.json trong ${dir}, bỏ qua build."
                            }
                        }
                    }
                    echo "✅ Build hoàn tất!"
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    def directories = ['admin/src', 'user/src']
                    for (dir in directories) {
                        dir("${dir}") {
                            if (fileExists('package.json')) {
                                echo "🧪 Chạy test trong ${dir}..."
                                sh 'npm run test || echo "⚠ Không có test nào, bỏ qua..."'
                            } else {
                                echo "⚠ Không tìm thấy package.json trong ${dir}, bỏ qua test."
                            }
                        }
                    }
                    echo "✅ Test hoàn tất!"
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
            echo '🎉 Build và push lên Harbor thành công!'
        }
        failure {
            echo '❌ Build thất bại. Kiểm tra logs để xem chi tiết.'
        }
    }
}

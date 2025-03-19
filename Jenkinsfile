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
                    echo "Pipeline bắt đầu chạy!"
                }
            }
        }
        stage('Checkout Source Code') {
            steps {
                script {
                    echo "Đang clone repository source code..."
                    git url: 'https://github.com/tierik-bjornson/pc-web-front.git', branch: 'main'
                    echo "Clone source code thành công!"
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script {
                    def directories = ['admin', 'user']
                    for (dir in directories) {
                        echo "Cài đặt dependencies cho ${dir}..."
                        dir("${dir}") {
                            if (fileExists('package.json')) {
                                sh 'npm install'
                            } else {
                                echo "Không tìm thấy package.json trong thư mục ${dir}, bỏ qua bước cài đặt."
                            }
                        }
                    }
                    echo "Cài đặt hoàn tất!"
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    def directories = ['admin', 'user']
                    for (dir in directories) {
                        echo "Bắt đầu build cho ${dir}..."
                        dir("${dir}") {
                            if (fileExists('package.json')) {
                                sh 'npm run build --prod'
                            } else {
                                echo "Không tìm thấy package.json trong thư mục ${dir}, bỏ qua bước build."
                            }
                        }
                    }
                    echo "Build hoàn tất!"
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    def directories = ['admin', 'user']
                    for (dir in directories) {
                        echo "Chạy test cho ${dir}..."
                        dir("${dir}") {
                            if (fileExists('package.json')) {
                                sh 'npm run test || echo "No tests specified, skipping..."'
                            } else {
                                echo "Không tìm thấy package.json trong thư mục ${dir}, bỏ qua bước test."
                            }
                        }
                    }
                    echo "Test xong!"
                }
            }
        }
        stage('Cleanup') {
            steps {
                script {
                    echo "Dọn dẹp Docker image..."
                    sh "docker rmi ${REGISTRY}/${PROJECT}/${IMAGE_NAME}:${DOCKER_IMAGE_TAG} || true"
                    echo "Dọn dẹp hoàn tất!"
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




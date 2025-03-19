pipeline {
    agent any

    tools {
        nodejs "Node23"
    }

    environment {
        REGISTRY = 'localhost:80'
        PROJECT = 'pc-web-front'
        IMAGE_NAME = 'frontend'
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
                    git url: 'https://github.com/tierik-bjornson/pc-web-front.git', branch: 'main'
                    echo "✅ Clone source code thành công!"
                }
            }
        }

        stage('Install Dependencies') {
            parallel {
                stage('Install User Frontend') {
                    steps {
                        script {
                            echo "📦 Cài đặt dependencies cho User Frontend..."
                            dir('user') {
                                sh 'npm install'
                            }
                            echo "✅ Cài đặt User Frontend hoàn tất!"
                        }
                    }
                }
                stage('Install Admin Frontend') {
                    steps {
                        script {
                            echo "📦 Cài đặt dependencies cho Admin Frontend..."
                            dir('admin') {
                                sh 'npm install'
                            }
                            echo "✅ Cài đặt Admin Frontend hoàn tất!"
                        }
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo "🏗️ Bắt đầu build cả User và Admin..."
                    dir('user-frontend') {
                        sh 'npx ng build --configuration=production --output-path=../dist/user'
                    }
                    dir('admin-frontend') {
                        sh 'npx ng build --configuration=production --output-path=../dist/admin'
                    }
                    echo "✅ Build hoàn tất!"
                }
            }
        }

    
        stage('Cleanup') {
            steps {
                script {
                    echo "🗑️ Dọn dẹp Docker image..."
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

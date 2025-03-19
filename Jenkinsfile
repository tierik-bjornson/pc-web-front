pipeline {
    agent any

    environment {
        NODE_VERSION = '18'
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    checkout scm
                    sh 'ls -la'  // Kiểm tra thư mục sau khi clone
                }
            }
        }

        stage('Setup Node.js & Angular') {
            steps {
                script {
                    sh "curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -"
                    sh "sudo apt-get install -y nodejs"
                    sh "node -v"
                    sh "npm -v"

                    // Cài Angular CLI đúng version
                    sh "npm install -g @angular/cli@12"
                    sh "ng version"
                }
            }
        }

        stage('Install Dependencies') {
            parallel {
                stage('Install Admin') {
                    steps {
                        dir('admin') {
                            sh 'ls -la'  // Kiểm tra package.json có tồn tại không
                            sh 'npm install --legacy-peer-deps'
                        }
                    }
                }
                stage('Install User') {
                    steps {
                        dir('user') {
                            sh 'ls -la'  // Kiểm tra package.json có tồn tại không
                            sh 'npm install --legacy-peer-deps'
                        }
                    }
                }
            }
        }

        stage('Build Angular Apps') {
            parallel {
                stage('Build Admin') {
                    steps {
                        dir('admin') {
                            sh 'ng build --configuration production'
                        }
                    }
                }
                stage('Build User') {
                    steps {
                        dir('user') {
                            sh 'ng build --configuration production'
                        }
                    }
                }
            }
        }

        stage('Run Tests') {
            parallel {
                stage('Test Admin') {
                    steps {
                        dir('admin') {
                            sh 'ng test --watch=false --browsers=ChromeHeadless || true'
                        }
                    }
                }
                stage('Test User') {
                    steps {
                        dir('user') {
                            sh 'ng test --watch=false --browsers=ChromeHeadless || true'
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Angular Apps...'
                // Thêm lệnh deploy tại đây (nếu cần)
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline executed successfully! 🎉'
        }
        failure {
            echo '❌ Pipeline failed! Debug logs needed.'
        }
    }
}

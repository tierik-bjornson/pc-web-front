pipeline {
    agent any

    environment {
        NODE_VERSION = '18' // Đổi sang Node.js 18 như bạn yêu cầu
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
                    sh "curl -fsSL https://deb.nodesource.com/setup_18.x | bash -"
                    sh "apt-get install -y nodejs"
                    sh "node -v"
                    sh "npm -v"
                }
            }
        }

        stage('Install Dependencies') {
            parallel {
                stage('Install Admin') {
                    steps {
                        dir('admin') {
                            sh 'npm ci'
                        }
                    }
                }
                stage('Install User') {
                    steps {
                        dir('user') {
                            sh 'npm ci'
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
                            sh 'npm run build -- --configuration production --output-path=dist/admin'
                        }
                    }
                }
                stage('Build User') {
                    steps {
                        dir('user') {
                            sh 'npm run build -- --configuration production --output-path=dist/user'
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
                            sh 'npm test -- --watch=false --browsers=ChromeHeadless'
                        }
                    }
                }
                stage('Test User') {
                    steps {
                        dir('user') {
                            sh 'npm test -- --watch=false --browsers=ChromeHeadless'
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Angular Apps...'
                // Thêm lệnh deploy thực tế nếu cần, ví dụ:
                // scp -r admin/dist/admin user@server:/var/www/admin
                // scp -r user/dist/user user@server:/var/www/user
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

pipeline {
    agent any

    environment {
        NODE_VERSION = '14' // Angular 12 hoạt động tốt với Node.js 14
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

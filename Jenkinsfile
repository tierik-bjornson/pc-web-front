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
                }
            }
        }

        stage('Setup Node.js') {
            steps {
                script {
                    sh "curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -"
                    sh "sudo apt-get install -y nodejs"
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
                            sh 'npm install'
                        }
                    }
                }
                stage('Install User') {
                    steps {
                        dir('user') {
                            sh 'npm install'
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
                            sh 'npm run build -- --configuration production'
                        }
                    }
                }
                stage('Build User') {
                    steps {
                        dir('user') {
                            sh 'npm run build -- --configuration production'
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
                // Ví dụ: scp hoặc rsync
                // sh 'scp -r admin/dist user@server:/var/www/admin'
                // sh 'scp -r user/dist user@server:/var/www/user'
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

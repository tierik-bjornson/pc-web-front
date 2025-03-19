pipeline {
    agent any
    
    environment {
        // Sử dụng Node 18 đã cài trong Jenkins
        NODE_VERSION = '18'
        ADMIN_PATH = 'admin'
        USER_PATH = 'user'
    }
    
    stages {
        stage('Preparation') {
            steps {
                // Sử dụng NodeJS đã cài trong Jenkins
                tool name: 'NodeJS', version: "${env.NODE_VERSION}"
                sh 'node --version'
                sh 'npm --version'
            }
        }
        
        stage('Install Dependencies') {
            parallel {
                stage('Admin Frontend') {
                    steps {
                        dir("${env.ADMIN_PATH}") {
                            sh 'npm install'
                        }
                    }
                }
                stage('User Frontend') {
                    steps {
                        dir("${env.USER_PATH}") {
                            sh 'npm install'
                        }
                    }
                }
            }
        }
        
        stage('Build') {
            parallel {
                stage('Build Admin') {
                    steps {
                        dir("${env.ADMIN_PATH}") {
                            sh 'npm run build'
                        }
                    }
                }
                stage('Build User') {
                    steps {
                        dir("${env.USER_PATH}") {
                            sh 'npm run build'
                        }
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying applications...'
                
                // Deploy admin
                dir("${env.ADMIN_PATH}/dist") {
                    // Ví dụ: deploy lên S3 hoặc server
                    // sh 'aws s3 sync . s3://your-admin-bucket --region your-region'
                    echo 'Deploying admin frontend...'
                }
                
                // Deploy user
                dir("${env.USER_PATH}/dist") {
                    // Ví dụ: deploy lên S3 hoặc server
                    // sh 'aws s3 sync . s3://your-user-bucket --region your-region'
                    echo 'Deploying user frontend...'
                }
            }
        }
    }
    
    post {
        success {
            echo 'Build and deployment completed successfully!'
        }
        failure {
            echo 'Build failed!'
        }
        always {
            cleanWs()
        }
    }
}

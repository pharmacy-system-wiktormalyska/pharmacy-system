pipeline {
    agent any
    options {
        skipDefaultCheckout()
    }
    environment {
        FRONTEND_IMAGE = 'pharmacy-system-frontend:latest'
        BACKEND_IMAGE = 'pharmacy-system-backend:latest'
    }

    stages{
        stage('Download main') {
            steps {
                git url: 'https://github.com/wiktormalyska/pharmacy-system.git',
                    branch: 'master',
                    credentialsId: 'github-wiktormalyska'
            }
        }

        stage('Download backend') {
            steps {
                dir('pharmacy-system-backend') {
                    git url: 'https://github.com/wiktormalyska/pharmacy-system-backend.git',
                        branch: 'master',
                        credentialsId: 'github-wiktormalyska'
                }
            }
        }

        stage('Download frontend') {
            steps {
                dir('pharmacy-system-frontend') {
                    git url: 'https://github.com/wiktormalyska/pharmacy-system-frontend.git',
                        branch: 'master',
                        credentialsId: 'github-wiktormalyska'
                }
            }
        } 

        stage('Input .env to backend') {
            steps {
                withCredentials([file(credentialsId: 'pharmacy-system-backend-.env', variable: 'BACKEND_ENV_FILE')]) {
                    sh 'cp "$BACKEND_ENV_FILE" pharmacy-system-backend/src/main/resources/.env'
                }
            }
        }
        
        stage('Verify Workspace') {
            steps {
                sh 'tree -a'
            }
        }

        
        //Build backend
        stage('Build backend') {
            steps {
                dir('pharmacy-system-backend') {
                    sh 'docker build -t $BACKEND_IMAGE .'
                }
            }
        }
        //Build frontend
        stage('Build frontend') {
            steps {
                dir('pharmacy-system-frontend') {
                    sh 'docker build -t $FRONTEND_IMAGE .'
                }
            }
        }
        //Deploy
        stage('Deploy') {
            steps {
                sh 'docker-compose down'
                sh 'docker-compose up -d'
            }
        }
    }
    //Always show logs
    post {
        always {
            script {
                sh 'docker-compose logs'
            }
        }
    }
}

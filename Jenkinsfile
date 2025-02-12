pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'flask-app'
        DOCKER_TAG = 'latest'
        CONTAINER_NAME = 'flask-webapp'
        DOCKER_REGISTRY_CREDS = 'docker-hub-credentials'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                        bat "docker build -t %DOCKER_IMAGE%:%DOCKER_TAG% ."
                    }
                }
            }
        }
        
        stage('Deploy Container') {
            steps {
                script {
                    bat "docker run -d -p 5000:5000 --name %CONTAINER_NAME% %DOCKER_IMAGE%:%DOCKER_TAG%"
                    bat "timeout /t 5 /nobreak >nul"
                }
            }
        }
    }
    
    post {
        always {
            bat "docker logout"
        }
        success {
            echo "Pipeline completed successfully! Application is running at http://localhost:5000"
        }
    }
}
pipeline {
    agent any 
    environment {
        DOCKERHUB_CREDENTIALS = credentials('test2')
    }
    stages {
        stage('Build docker image') {
            steps {  
                echo "Building Docker image..."
                sh 'docker build -t rajeshb21/app:$BUILD_NUMBER .'
            }
        }
        stage('login to dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'test2', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    echo "Logging in to Docker Hub..."
                    sh "docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD"
                }
            }
        }
        stage('push image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                sh 'docker push rajeshb21/app:$BUILD_NUMBER'
            }
        }
        stage('Deploy to Staging') {
            steps {
                sh 'docker pull rajeshb21/app:$BUILD_NUMBER'
                sh 'docker stop myapp-container || true'
                sh 'docker rm myapp-container || true'
                sh 'docker run -d --name myapp-container -p 8000:8000 rajeshb21/app:$BUILD_NUMBER'
                // Place docker logout here, not in post!
                sh 'docker logout'
            }
        }
    }
    post {
        always {
            echo 'Build completed.'
        }
    }
}

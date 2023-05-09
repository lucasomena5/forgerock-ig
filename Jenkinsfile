pipeline {
    agent any
    
    stages {
        stage('Authenticate with Docker Hub') {
		    steps {
		        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
		            sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
		        }
		    }
		}
        
        stage('Build Docker Image') {
            steps {
                sh 'echo "Building Docker image..."'
                sh 'docker build -t my-app .'
            }
        }
    }
}

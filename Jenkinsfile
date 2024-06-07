pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "your-dockerhub-username/your-repo-name"
        DOCKER_TAG = "${env.BUILD_ID}"
    }

    stages {
        stage('Build') {
            steps {
                checkout scm
                sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
            }
        }
        
        stage('Test') {
            steps {
                sh 'docker run --rm ${DOCKER_IMAGE}:${DOCKER_TAG} npm test'
            }
        }
        
        stage('Push') {
            steps {
                sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                sh 'docker push ${DOCKER_IMAGE}:${DOCKER_TAG}'
            }
        }
        
        stage('Deploy') {
            steps {
                sshagent(['your-ssh-credentials-id']) {
                    sh """
                    ssh user@deployment-server '
                    docker pull ${DOCKER_IMAGE}:${DOCKER_TAG} &&
                    docker stop your-repo-name || true &&
                    docker rm your-repo-name || true &&
                    docker run -d --name your-repo-name -p 80:3000 ${DOCKER_IMAGE}:${DOCKER_TAG}
                    '
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

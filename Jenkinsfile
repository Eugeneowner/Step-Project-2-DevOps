pipeline {
    agent { label 'jenkins-worker' }
    
    environment {
        GIT_URL = "https://github.com/Eugeneowner/Step-Project-2-DevOps.git"
        GIT_BRANCH = "main"
        DOCKER_IMAGE_NAME = "your-docker-id/step-project-2-devops"
        DOCKER_REGISTRY_URL = "https://index.docker.io/v1/"
        DOCKER_CREDENTIALS_ID = "docker-hub"
        GITHUB_CREDENTIALS_ID = "github-creds"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: "${GITHUB_CREDENTIALS_ID}", url: "${GIT_URL}", branch: "${GIT_BRANCH}"
            }
        }
      
        stage('Build Docker Image') {
            steps {
                script {
                    def app = docker.build("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}")
                }
            }
        }
     
        stage('Run Tests') {
            steps {
                script {
                    sh '''
                        docker run --rm --name step_project_2 ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} npm test || { echo "Tests failed"; exit 1; }
                    '''
                }
            }
        }

        stage('Push to Docker Hub') {
            when {
                expression { return currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                        sh "docker push ${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
                    }
                }
            }
        }
    }

    post {
        failure {
            echo "❌ Tests failed! Build stopped."
        }
        success {
            echo "✅ Docker image successfully pushed to Docker Hub!"
        }
    }
}
pipeline {
    agent any
    
    environment {
     IMAGE_NAME = "node-js-sample"    
     IMAGE_TAG = "1.0"
     DOCKERFILE_PATH = "node-js-sample/"
    }
    
    stages {
        stage("Clone Repository"){
            steps {
                script {
                    git branch: "${BRANCH}",
                    url: "${GIT_REPO_URL}"
                }
            }    
        }
        
        stage("Build Docker Image") {
            steps{
                script {
                    sh 'cd node-js-sample && docker build -t node-js-sample:1.0 .'   
                    // app = docker.build("${IMAGE_NAME}:${IMAGE_TAG}","${DOCKERFILE_PATH}")
                }
            }
        }
        
        stage("Push Docker Image") {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USER')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKERHUB_USER -p $DOCKERHUB_PASSWORD'
                        sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} aditivarade/${IMAGE_NAME}:${IMAGE_TAG}"
                        sh 'docker push aditivarade/${IMAGE_NAME}:${IMAGE_TAG}'
                    }
                    // docker.withRegistry('https//registry.hub.docker.com', 'docker-hub') {
                    //     app.push("${IMAGE_TAG}")
                    // }
                }
            }
        }
    }
}
pipeline {
    agent any
    
    environment {
     IMAGE_NAME = "node-js-sample"    
     IMAGE_TAG = "1.0"
     DOCKERFILE_PATH = "./"
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
                    app = docker.build("${IMAGE_NAME}:${IMAGE_TAG}","${DOCKERFILE_PATH}")
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
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl config get-contexts"
                    sh "kubectl config use-context minikube"
                    sh "kubectl apply -f k8s/deployment.yaml"
                    sh "kubectl apply -f k8s/persistent-volume.yaml"
                    sh "kubectl apply -f k8s/network-policy.yaml"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh "kubectl get pods -o wide"
                    sh "kubectl get svc"
                    sh "kubectl get pv"
                    sh "kubectl get pvc"
                    sh "kubectl get networkpolicy"
                    sh "kubectl get pods --show-labels"   
                }
            }
        }
    }
}
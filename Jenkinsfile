pipeline {
    agent any

    states {

        stage ('Build Docker Image'){
            steps {
                script {
                    dockerapp = docker.build("galladas/kube-news:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        state ('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com','dockerhub')
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                }
            }
        }
        state ('Deploy Kubernetes') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                withKubeConfig ([credentialsId: 'kubeconfig']) {
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                }
            }
        }
    }
}
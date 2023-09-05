pipeline {
    environment {
        DOCKER_ID = "azzouz953" // Utilisez votre propre Docker ID.
        DOCKER_TAG = "latest" // J'utilise "latest" puisque c'est le tag que vous avez montré précédemment.
    }

    agent any

    stages {
        stage('Pull Docker Images') {
            steps {
                script {
                    sh '''
                    docker pull $DOCKER_ID/cast-service:$DOCKER_TAG
                    docker pull $DOCKER_ID/movie-service:$DOCKER_TAG
                    '''
                }
            }
        }
    }
     stage('Deploy to Dev') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_PATH')]) {
                    sh '''
                    export KUBECONFIG=$KUBECONFIG_PATH
                    helm upgrade --install cast ./cast-service/cast-service --namespace dev
                    helm upgrade --install movie ./movie-service/movie-service --namespace dev
                    '''
                }
            }
        }



}

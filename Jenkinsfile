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
}

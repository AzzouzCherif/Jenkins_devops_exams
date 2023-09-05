pipeline {
    environment {
        DOCKER_ID = "azzouz953"
        DOCKER_TAG = "latest" // Utilisation de 'latest' pour simplifier, bien que vous pourriez vouloir utiliser BUILD_ID ou d'autres tags si nécessaire
    }
    agent any

    stages {
        stage('Docker Pull for cast-service') {
            steps {
                sh 'docker pull $DOCKER_ID/cast-service:$DOCKER_TAG'
            }
        }
        
        stage('Docker Pull for movie-service') {
            steps {
                sh 'docker pull $DOCKER_ID/movie-service:$DOCKER_TAG'
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

        stage('Deploy to QA') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_PATH')]) {
                    sh '''
                    export KUBECONFIG=$KUBECONFIG_PATH
                    helm upgrade --install cast ./cast-service/cast-service --namespace qa
                    helm upgrade --install movie ./movie-service/movie-service --namespace qa
                    '''
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_PATH')]) {
                    sh '''
                    export KUBECONFIG=$KUBECONFIG_PATH
                    helm upgrade --install cast ./cast-service/cast-service --namespace staging
                    helm upgrade --install movie ./movie-service/movie-service --namespace staging
                    '''
                }
            }
        }

        stage('Wait for Production Approval') {
            when {
                allOf {
                    expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
                    branch 'master'
                }
            }
            steps {
                input message: 'Deploy to Production?', ok: 'Deploy'
            }
        }

        stage('Deploy to Production') {
            when {
                allOf {
                    expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
                    branch 'master'
                }
            }
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_PATH')]) {
                    sh '''
                    export KUBECONFIG=$KUBECONFIG_PATH
                    helm upgrade --install cast ./cast-service/cast-service --namespace prod
                    helm upgrade --install movie ./movie-service/movie-service --namespace prod
                    '''
                }
            }
        }
    }
}

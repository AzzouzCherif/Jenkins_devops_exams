####fin
pipeline {
    agent any

    stages {
        stage('Build & Test') {
            steps {
		sh 'docker pull azzouz953/cast-service:latest'
		sh 'docker pull azzouz953/movie-service:latest'
            }
        }

        stage('Deploy to Dev') {
            steps {
                sh 'helm upgrade --install cast ./cast-service/cast-service --namespace dev'
                sh 'helm upgrade --install nginx ./nginx-service/nginx-service --namespace dev'
                sh 'helm upgrade --install movie ./movie-service/movie-service --namespace dev'
            }
        }

        stage('Deploy to QA') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                sh 'helm upgrade --install cast ./cast-service/cast-service --namespace qa'
                sh 'helm upgrade --install nginx ./nginx-service/nginx-service --namespace qa'
                sh 'helm upgrade --install movie ./movie-service/movie-service --namespace qa'
            }
        }

        stage('Deploy to Staging') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                sh 'helm upgrade --install cast ./cast-service/cast-service --namespace staging'
                sh 'helm upgrade --install nginx ./nginx-service/nginx-service --namespace staging'
                sh 'helm upgrade --install movie ./movie-service/movie-service --namespace staging'
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
                sh 'helm upgrade --install cast ./cast-service/cast-service --namespace prod'
                sh 'helm upgrade --install nginx ./nginx-service/nginx-service --namespace prod'
                sh 'helm upgrade --install movie ./movie-service/movie-service --namespace prod'
            }
        }
    }
}

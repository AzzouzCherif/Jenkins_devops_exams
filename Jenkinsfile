pipeline {
    agent any

    stages {
        stage('Build & Test') {
            steps {
		sh 'docker pull azzouz953/cast-service:latest'
		sh 'docker pull azzouz953/movie-service:latest'
            }
        }


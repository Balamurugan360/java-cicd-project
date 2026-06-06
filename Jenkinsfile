pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Maven Project...'
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker Image...'
                sh 'docker build -t java-cicd-project:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Container...'

                sh '''
                docker stop java-app || true
                docker rm java-app || true

                docker run -d \
                --name java-app \
                -p 8080:8080 \
                java-cicd-project:${BUILD_NUMBER}
                '''
            }
        }
    }
}
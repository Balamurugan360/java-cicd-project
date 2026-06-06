pipeline {

    agent any

    environment {

        IMAGE_NAME = "dockerhubuser/java-cicd-project"

    }

    stages {

        stage('Checkout') {

            steps {

                git 'https://github.com/username/java-cicd-project.git'
            }
        }

        stage('Build') {

            steps {

                sh 'mvn clean package'

            }
        }

        stage('SonarQube Scan') {

            steps {

                withSonarQubeEnv('SonarQube') {

                    sh '''
                    mvn sonar:sonar \
                    -Dsonar.projectKey=java-cicd-project
                    '''
                }
            }
        }

        stage('Docker Build') {

            steps {

                sh '''
                docker build -t $IMAGE_NAME:$BUILD_NUMBER .
                '''
            }
        }

        stage('Docker Push') {

            steps {

                withCredentials([
                    usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    docker push $IMAGE_NAME:$BUILD_NUMBER
                    '''
                }
            }
        }

        stage('Deploy') {

            steps {

                sh '''

                docker stop java-app || true

                docker rm java-app || true

                docker run -d \
                --name java-app \
                -p 8080:8080 \
                $IMAGE_NAME:$BUILD_NUMBER

                '''
            }
        }

    }
}

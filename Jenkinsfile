pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('suhanigoel24') // Jenkins credentials ID
        DOCKER_IMAGE = "suhanigoel24/paytm-registration"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/suhanigoel24/JenkinsProj.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
                }
            }
        }

        stage('Login to DockerHub') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials',
                                          usernameVariable: 'DOCKER_USER',
                                          passwordVariable: 'DOCKER_PASS')]) {
            sh '''
                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            '''
        }
    }
}

        stage('Push to DockerHub') {
            steps {
                script {
                    sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
                    sh 'docker tag $DOCKER_IMAGE:$BUILD_NUMBER $DOCKER_IMAGE:latest'
                    sh 'docker push $DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy on Local Docker') {
            steps {
                script {
                    sh 'docker rm -f paytm-registration || true'
                    sh 'docker run -d --name paytm-registration -p 8081:80 $DOCKER_IMAGE:latest'
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished."
        }
    }
}

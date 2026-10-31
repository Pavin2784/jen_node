pipeline {
    agent any

    environment {
        IMAGE = 'pavinb/node'
        DB_HOST = 'mysql'
        DB_NAME = 'node'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm 
        }

        stage('Build and Run with Docker Compose') {
            steps {
                dir('app') {
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'mysql',
                            usernameVariable: 'DB_USER',
                            passwordVariable: 'DB_PASS'
                        )
                    ]) {
                        sh '''
                            docker compose down || true
                            docker compose up -d --build
                        '''
                    }
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh '''
                    docker push ${IMAGE}:v${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }

}

}













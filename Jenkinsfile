pipeline {
    agent any

    environment {
    PATH = "/Users/lateeshawilliams/.docker/bin:${env.PATH}"
}

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t hello .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )]) {
                    sh '''
                        echo "$DOCKER_TOKEN" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag hello lateeshawilliams/hello:latest
                        docker push lateeshawilliams/hello:latest
                    '''
                }
            }
        }
stage('Deploy to EC2') {
    steps {
        withCredentials([sshUserPrivateKey(
            credentialsId: 'ec2-creds',
            keyFileVariable: 'SSH_KEY',
            usernameVariable: 'SSH_USER'
        )]) {
            sh '''
            sudo docker pull lateeshawilliams/hello:latest &&
            sudo docker stop hello || true &&
            sudo docker rm hello || true &&
            sudo docker run -d --name hello -p 8080:8080 lateeshawilliams/hello:latest
                "
            '''
        }
    }
}
    }
}
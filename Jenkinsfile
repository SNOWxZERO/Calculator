pipeline {
    agent none

    environment {
        DOCKER_CREDS = credentials('dockerhub-cred')
        IMAGE_NAME = "snowxzero/simple-calc:latest"
    }

    stages {
        stage('Build & Push Image') {
            agent { label 'docker-ssh-agent' }
            steps {
                unstash 'source'
                sh "docker build -t $IMAGE_NAME ."
                sh 'echo $DOCKER_CRED_PSW | docker login -u $DOCKER_CRED_USR --password-stdin'
                sh "docker push $IMAGE_NAME"
            }
        }

        stage('Deploy on Docker Agent') {
            agent { label 'docker-ssh-agent' }
            steps {
                sh 'docker rm -f calc-app || true'
                sh "docker run -d -p 30000:3000 --name calc-app $IMAGE_NAME"
            }
        }

        stage('Deploy on AWS Agent') {
            agent { label 'aws-agent' }
            steps {
                sh 'docker rm -f calc-app || true'
                sh "docker run -d -p 30000:3000 --name calc-app $IMAGE_NAME"
            }
        }
    }
}

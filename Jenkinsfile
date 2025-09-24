pipeline {
    agent none

    environment {
        IMAGE_NAME   = "snowxzero/simple-calc:latest"
    }

    stages {
        stage('Echo Branch') {
            steps {
                echo "Building branch: ${env.BRANCH_NAME}"
            }
        }
        stage('Build & Push Image') {
            agent { label 'docker-ssh-agent' }
            steps {
                sh "docker build -t $IMAGE_NAME ."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', 
                                  usernameVariable: 'DOCKER_USER', 
                                  passwordVariable: 'DOCKER_PASS')]) {
                sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
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

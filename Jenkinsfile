pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = "caleb2023"
        DOCKER_HUB_PASSWORD = "dckr_pat_esOJHDDYVzZy714PS0hscyZ6jDI"
        DOCKER_IMAGE_NAME = "helloworld"
        DOCKER_IMAGE_TAG = "latest"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'ls -l /usr/local'
                sh "export PATH=$PATH:/usr/local/bin"
                sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ."
                // sh "docker tag ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh "docker tag ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
            }
        }

        stage('Push Docker Image') {
            steps {
                // withCredentials([[$class: 'UsernamePasswordMultiBinding', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD']]) {
                    
                // }
                sh "docker login -u ${DOCKER_HUB_USERNAME} -p ${DOCKER_HUB_PASSWORD}"
                sh "docker push ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
            }
        }
    }
}

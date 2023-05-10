pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = "caleb2023"
        DOCKER_HUB_PASSWORD = "dckr_pat_esOJHDDYVzZy714PS0hscyZ6jDI"
        DOCKER_IMAGE_NAME = "helloworld"
        DOCKER_IMAGE_TAG = "latest"
        HELM_RELEASE = "helloworld"
    }

    stages {



        stage('Push Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ."
                sh "docker login -u ${DOCKER_HUB_USERNAME} -p ${DOCKER_HUB_PASSWORD}"
                sh "docker tag ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} caleb2023/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                sh "docker push caleb2023/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
            }
        }

        stage(' Deploy Dev') {
          when { not {anyOf {branch 'staging'; branch 'master'} } }
          steps{
            deploy("dev", "values.yaml", env.GIT_COMMIT, "--debug")
          }
        }

        stage(' Deploy Staging') {
          when { branch 'staging'}
          steps{
            deploy("staging", "values-staging.yaml", env.GIT_COMMIT, "--debug")
          }
        }

        stage(' Deploy Prod') {
          when { branch 'main'}
          steps{
            deploy("prod", "values-prod.yaml", env.GIT_COMMIT, "--debug")
          }
        }
    }
}

def deploy(env, values_file, image_tag, extra_args = "") {
    script{
      sh """
      helm dependency update charts/${HELM_RELEASE}
      helm upgrade ${HELM_RELEASE} charts/${HELM_RELEASE} -n ${env} \
      -f charts/${HELM_RELEASE}/values.yaml -f charts/${HELM_RELEASE}/${values_file} \
      --set service.image.tag=${image_tag} --install --atomic ${extra_args}
      """
    }
}

def timeDiff(st) {
    def delta = (new Date()).getTime() - st.getTime()
    def seconds = delta.intdiv(1000) % 60
    def minutes = delta.intdiv(60 * 1000) % 60
    return "${minutes} min ${seconds} sec"
}

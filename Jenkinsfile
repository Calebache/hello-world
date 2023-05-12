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
          when { branch 'master'}
          steps{
            deploy("prod", "values-prod.yaml", env.GIT_COMMIT, "--debug")
          }
        }
    }
}

def deploy(env, values_file, image_tag, extra_args = "") {
    script{
        sh '''
        az login --service-principal -u 588dc2cf-b5c0-4188-819b-09a346943ffd -p 87h8Q~PE1jReaKupR4Ob0rFmNxMvde.sEaURBaT6 --tenant 706748a9-ed1d-4a90-8a87-ef1594d6ca25
        az aks get-credentials --resource-group cloudesence-prod-RG --name cloudesence-prod
        '''
      sh """
      helm dependency update chartz/${HELM_RELEASE}
      helm upgrade ${HELM_RELEASE} chartz/${HELM_RELEASE} -n ${env} \
      -f chartz/${HELM_RELEASE}/values.yaml -f chartz/${HELM_RELEASE}/${values_file} \
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

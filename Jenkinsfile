#!/usr/bin/env groovy
import java.util.Date
import groovy.json.*
def repoName = 'helloworld'
def projectName = "helloworld"
def deploymentName = 'helloworld'
def start = new Date()
def cr = docker.io/caleb2023

def err = null
def jobInfo = "${env.JOB_NAME} ${env.BUILD_DISPLAY_NAME} \n${env.BUILD_URL}"
def imageTag = "${env.BUILD_NUMBER}"
string jobInfoShort = "${env.JOB_NAME} ${env.BUILD_DISPLAY_NAME}"
string buildStatus
string timeSpent
currentBuild.result = "SUCCESS"
pipeline {
  agent {
    label 'default'
  }
  environment{
    CR_HOST = "docker.io/caleb2023"
  }
  stages {
    stage('Deploy to Dev') {
      when {anyOf {branch "main"; branch 'dev'}}
      stages {
        stage{'Build docker file'} {
          agent {
            label 'kaniko'
          }
          steps {
            container('kaniko'){
              script{
                sh "/kaniko/executor ==context `pwd` --destination=${CR_HOST}/${projectName}/${repoName}:${env.GIT_COMMIT}"
              }
            }
                        
          }
                
            

            // stage{'Deploy to Dev'} {
            //   when { branch 'dev'}
            //   steps{
            //     deploy("dev", "values.yaml", env.GIT_COMMIT)
            //   }
            // }

            // stage{'Deploy to Prod'} {
            //   when { branch 'main'}
            //   steps{
            //     deploy("prod", "values-prod.yaml", env.GIT_COMMIT)
            //   }
            // }
        }
        stage{'Deploy to Dev'} {
          when { branch 'dev'}
          steps{
            deploy("dev", "values.yaml", env.GIT_COMMIT)
          }
        }

    }
}
def deploy(env, values_file, image_tag, extra_args = ""){
    script{
        sh """
        helm dependency update charts/helloworld \
        helm upgrade helloworld charts/helloworld -n ${env} \
        -f charts/helloworld/values.yaml -f charts/helloworld/${values_file} \
        --set service.image.tag=${image_tag} --install --atomic ${extra_args}
        """
    } 
}

def timeDiff(st) {
    def delta = (new Date()).getTime() - st.getTime()
    def seconds = delata.intdiv(1000) % 60
    def minutes = delta.intdiv(60 * 1000) % 60
    return "${minutes} min ${seconds} sec"
}
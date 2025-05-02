pipeline {
  agent none

  environment {
    REGISTRY_URL = "docker-registry.docker-registry.svc.cluster.local:5000"
    IMAGE_TAG = "01"
    GIT_URL = "https://github.com/Samsonnik/k8s-final.git"
  }

  stages {

    stage("Build & Push Front") {
      agent {
        kubernetes {
          yamlFile 'kaniko-builder.yaml'
        }
      }

      steps {
        git branch: 'main', url: "${GIT_URL}"

        script {
          buildAndPushImage("8.images/1.front", "8.images/1.front/dockerfile", "front")
        }
      }
    }

    stage("Build & Push Back") {
      agent {
        kubernetes {
          yamlFile 'kaniko-builder.yaml'
        }
      }

      steps {
        git branch: 'main', url: "${GIT_URL}"

        script {
          buildAndPushImage("8.images/2.back", "8.images/2.back/dockerfile", "back")
        }
      }
    }

    stage("Deploy Front") {
      agent {
        kubernetes {
          yamlFile 'helm-builder.yaml'
        }
      }

      steps {
        git branch: 'main', url: "${GIT_URL}"

        script {
          container('helm') {
            sh """
              helm upgrade --install front-release helm/front \
                --namespace app \
                --create-namespace \
                --set image.repository=${REGISTRY_URL}/front \
                --set image.tag=${IMAGE_TAG} \
                --set image.pullPolicy=IfNotPresent
            """
          }
        }
      }
    }

    stage("Deploy Back") {
      agent {
        kubernetes {
          yamlFile 'helm-builder.yaml'
        }
      }

      steps {
        git branch: 'main', url: "${GIT_URL}"

        script {
          container('helm') {
            sh """
              helm upgrade --install back-release helm/back \
                --namespace app \
                --set image.repository=${REGISTRY_URL}/back \
                --set image.tag=${IMAGE_TAG} \
                --set image.pullPolicy=IfNotPresent
            """
          }
        }
      }
    }
  }
}

def buildAndPushImage(String contextPath, String dockerfilePath, String imageName) {
  container('kaniko') {
    sh """
      /kaniko/executor \
        --context=`pwd`/${contextPath} \
        --dockerfile=`pwd`/${dockerfilePath} \
        --destination=${env.REGISTRY_URL}/${imageName}:${env.IMAGE_TAG} \
        --skip-tls-verify=true \
        --cache=true \
        --cache-repo=${env.REGISTRY_URL}/kaniko-cache/${imageName}
    """
  }
}

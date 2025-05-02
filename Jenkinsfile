pipeline {
  agent none

  environment {
    REGISTRY_URL = "docker-registry.docker-registry.svc.cluster.local:5000"
    IMAGE_TAG = "latest"
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
        git branch: 'main', url: ${GIT_URL}
        script {
          buildAndPushImage("8.images/2.back", "8.images/2.back/dockerfile", "back")
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
        git branch: 'main', url: ${GIT_URL}
        script {
          buildAndPushImage("8.images/2.back", "8.images/2.back/dockerfile", "back")
        }       
      }
    }
  }

def buildAndPushImage(String contextPath, String dockerfilePath, String imageName) {
  container('kaniko') {
    sh """
      /kaniko/executor \\
        --context=`pwd`/${contextPath} \\
        --dockerfile=`pwd`/${dockerfilePath} \\
        --destination=${REGISTRY_URL}/${imageName}:${IMAGE_TAG} \\
        --insecure-registries=${REGISTRY_URL} \\
        --skip-tls-verify=true
    """
  }
}

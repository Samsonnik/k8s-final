pipeline {
  agent {
    kubernetes {
      yamlFile 'kaniko-builder.yaml'
    }
  }

  environment {
    REGISTRY_URL = "docker-registry.docker-registry.svc.cluster.local:5000"
    FRONT_IMAGE_NAME = "front"
    BACK_IMAGE_NAME = "back"
    IMAGE_TAG = "01"
  }

  stages {
    stage("Checkout Code") {
      steps {
        git branch: 'main', url: 'https://github.com/Samsonnik/k8s-final.git'
      }
    }

    stage("Build & Push Front") {
      steps {
        script {
          buildAndPushImage("8.images/1.front", "8.images/1.front/dockerfile", "front")
        }
      }
    }

    stage("Build & Push Back") {
      steps {
        script {
          buildAndPushImage("8.images/2.back", "8.images/2.back/dockerfile", "back")
        }
      }
    }
  }

  options {
    disableConcurrentBuilds()
  }
}

def buildAndPushImage(String contextPath, String dockerfilePath, String imageName) {
  container('kaniko') {
    sh """
      /kaniko/executor \\
        --context=`pwd`/${contextPath} \\
        --dockerfile=`pwd`/${dockerfilePath} \\
        --destination=${REGISTRY_URL}/${imageName}:${IMAGE_TAG} \\
        --skip-tls-verify=true
    """
  }
}

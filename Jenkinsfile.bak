pipeline {
  agent {
    kubernetes {
      yamlFile 'kaniko-builder.yaml'
    }
  }

  environment {
    REGISTRY_URL = "docker-registry.docker-registry.svc.cluster.local:5000"
    BACK_IMAGE_NAME = "back"
    FRONT_IMAGE_NAME = "front"
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
        container('kaniko') {
          sh """
            /kaniko/executor \
              --context=`pwd`/8.images/1.front \
              --dockerfile=`pwd`/8.images/1.front/dockerfile \
              --destination=${REGISTRY_URL}/${FRONT_IMAGE_NAME}:${IMAGE_TAG} \
              --skip-tls-verify=true
          """
        }
      }
    }

    stage("Build & Push Back") {
      steps {
        container('kaniko') {
          sh """
            /kaniko/executor \
              --context=`pwd`/8.images/2.back \
              --dockerfile=`pwd`/8.images/2.back/dockerfile \
              --destination=${REGISTRY_URL}/${BACK_IMAGE_NAME}:${IMAGE_TAG} \
              --skip-tls-verify=true
          """
        }
      }
    }
  }
}

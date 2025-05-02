pipeline {
  agent {
    kubernetes {
      yamlFile 'kaniko-builder.yaml'
    }
  }

  environment {
    APP_NAME = "front"
    IMAGE_NAME = "docker-registry.docker-registry.svc.cluster.local:5000/${APP_NAME}"
    IMAGE_TAG = "01"
  }

  stages {
    stage("Checkout Code") {
      steps {
        git branch: 'main', url: 'https://github.com/Samsonnik/k8s-final.git'
      }
    }
    
    stage("Check YAML") {
      steps {
        sh 'cat kaniko-builder.yaml'
      }
    }

    stage("Build & Push with Kaniko") {
      steps {
        container('kaniko') {
          sh """
            /kaniko/executor \
              --context=`pwd`/8.images/1.front \
              --dockerfile=`pwd`/8.images/1.front/dockerfile \
              --destination=${IMAGE_NAME}:${IMAGE_TAG} \
              --insecure-registries=docker-registry.docker-registry.svc.cluster.local:5000 \
              --skip-tls-verify=true
          """
        }
      }
    }
  }
}

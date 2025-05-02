pipeline {
  agent none

  environment {
    REGISTRY_URL = "docker-registry.docker-registry.svc.cluster.local:5000"
    IMAGE_TAG = "latest"
  }

  stages {
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
}

def buildAndPushImage(String contextPath, String dockerfilePath, String imageName) {
  podTemplate(
    yamlFile: 'kaniko-builder.yaml'
  ) {
    node {
      stage("Clone and Build ${imageName}") {
        git branch: 'main', url: 'https://github.com/Samsonnik/k8s-final.git'

        container('kaniko') {
          sh """
            /kaniko/executor \
              --context=`pwd`/${contextPath} \
              --dockerfile=`pwd`/${dockerfilePath} \
              --destination=${env.REGISTRY_URL}/${imageName}:${env.IMAGE_TAG} \
              --skip-tls-verify=true
          """
        }
      }
    }
  }
}

pipeline {
  agent {
    kubernetes {
      yamlFile 'kaniko-builder.yaml'
    }
  }

  environment {
    PRIVATE_REGISTRY = "docker-registry.docker-registry.svc.cluster.local:5000"
    APP_NAME = "front"
    IMAGE_NAME = "${env.PRIVATE_REGISTRY}/${env.APP_NAME}"
    IMAGE_TAG = "01"
  }

  stages {
    stage("Checkout Code") {
      steps {
        git branch: 'main', url: 'https://github.com/Samsonnik/k8s-final.git'
      }
    }

    stage("Build & Push with Kaniko") {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'private-registry-credentials',
          usernameVariable: 'REGISTRY_USERNAME',
          passwordVariable: 'REGISTRY_PASSWORD'
        )]) {
          container('kaniko') {
            script {
              sh """
                mkdir -p /home/jenkins/.docker
                echo "{\"auths\":{\"$PRIVATE_REGISTRY\":{\"username\":\"$REGISTRY_USERNAME\",\"password\":\"$REGISTRY_PASSWORD\"}}}" > /home/jenkins/.docker/config.json
                export DOCKER_CONFIG=/home/jenkins/.docker
                /kaniko/executor \
                  --context=`pwd`/8.images/1.front \
                  --dockerfile=`pwd`/8.images/1.front/dockerfile \
                  --destination=${IMAGE_NAME}:${IMAGE_TAG} \
                  --skip-tls-verify=true \
              """
            }
          }
        }
      }
    }
  }
}

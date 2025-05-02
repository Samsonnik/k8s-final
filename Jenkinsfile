pipeline {
  agent none   // ← не указываем агент глобально

  environment {
    REGISTRY_URL = "docker-registry.docker-registry.svc.cluster.local:5000"
    IMAGE_TAG = "latest"
  }

  stages {
    stage("Build & Push Front") {
      agent {
        kubernetes {
          yamlFile 'kaniko-builder.yaml'   // 📁 используем YAML
        }
      }

      steps {
        git branch: 'main', url: 'https://github.com/Samsonnik/k8s-final.git'

        container('kaniko') {   // 🛠 переключаемся на sidecar-контейнер kaniko
          sh """
            /kaniko/executor \
              --context=`pwd`/8.images/1.front \
              --dockerfile=`pwd`/8.images/1.front/dockerfile \
              --destination=${REGISTRY_URL}/front:${IMAGE_TAG} \
              --skip-tls-verify=true
          """
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
        git branch: 'main', url: 'https://github.com/Samsonnik/k8s-final.git'

        container('kaniko') {
          sh """
            /kaniko/executor \
              --context=`pwd`/8.images/2.back \
              --dockerfile=`pwd`/8.images/2.back/dockerfile \
              --destination=${REGISTRY_URL}/back:${IMAGE_TAG} \
              --skip-tls-verify=true
          """
        }
      }
    }
  }
}

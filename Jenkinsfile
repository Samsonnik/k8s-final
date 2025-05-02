pipeline {
  agent none

  environment {
    REGISTRY_URL = "docker-registry.docker-registry.svc.cluster.local:5000"
    IMAGE_TAG = "latest"
  }

  stages {
    stage('Checkout Code') {
      steps {
        script {
          try {
            git branch: 'main', url: 'https://github.com/Samsonnik/k8s-final.git'
          } catch (e) {
            echo "Ошибка при клонировании репозитория: ${e}"
            currentBuild.result = 'FAILURE'
            throw e
          }
        }
      }
    }

    stage('Build Front') {
      steps {
        script {
          buildAndPushImage("8.images/1.front", "8.images/1.front/dockerfile", "front")
        }
      }
    }

    stage('Build Back') {
      steps {
        script {
          buildAndPushImage("8.images/2.back", "8.images/2.back/dockerfile", "back")
        }
      }
    }
  }
}

// 🛠 Общая функция для сборки
def buildAndPushImage(String contextPath, String dockerfilePath, String imageName) {
  podTemplate(
    label: "kaniko-${imageName}",
    yamlFile: 'kaniko-builder.yaml'
  ) {
    node("kaniko-${imageName}") {
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

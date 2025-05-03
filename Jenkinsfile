pipeline {
  agent {
    kubernetes {
      yamlFile 'agent.yaml'
      defaultContainer 'agent'
      customWorkspace '/workspace'
    }
  }
  stages {
    stage("Download") {
      steps {
        sh "git clone https://github.com/Samsonnik/k8s-final.git ."
      }
    }
  }
}

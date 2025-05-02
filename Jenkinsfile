pipeline {
  agent none

  environment {
    REGISTRY_URL = "docker-registry.docker-registry.svc.cluster.local:5000"
    IMAGE_TAG = "latest"
  }

  stages {
    stage("Build & Push Front") {
      steps {
        podTemplate(
          label: 'kaniko-front',
          containers: [
            containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent:latest'),
            containerTemplate(
              name: 'kaniko',
              image: 'gcr.io/kaniko-project/executor:debug',
              command: '/busybox/sh',
              args: '-c while true; do echo Waiting...; sleep 10; done'
            )
          ],
          volumes: [
            secretVolume(mountPath: '/kaniko/.docker', secretName: 'my-registry-secret')
          ]
        ) {
          node('kaniko-front') {
            stage("Clone and Build Front") {
              git branch: 'main', url: 'https://github.com/Samsonnik/k8s-final.git'

              container('kaniko') {
                sh """
                  /kaniko/executor \
                    --context=`pwd`/8.images/1.front \
                    --dockerfile=`pwd`/8.images/1.front/dockerfile \
                    --destination=${REGISTRY_URL}/${IMAGE_TAG} \
                    --skip-tls-verify=true
                """
              }
            }
          }
        }
      }
    }

    stage("Build & Push Back") {
      steps {
        podTemplate(
          label: 'kaniko-back',
          containers: [
            containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent:latest'),
            containerTemplate(
              name: 'kaniko',
              image: 'gcr.io/kaniko-project/executor:debug',
              command: '/busybox/sh',
              args: '-c while true; do echo Waiting...; sleep 10; done'
            )
          ],
          volumes: [
            secretVolume(mountPath: '/kaniko/.docker', secretName: 'my-registry-secret')
          ]
        ) {
          node('kaniko-back') {
            stage("Clone and Build Back") {
              git branch: 'main', url: 'https://github.com/Samsonnik/k8s-final.git'

              container('kaniko') {
                sh """
                  /kaniko/executor \
                    --context=`pwd`/8.images/2.back \
                    --dockerfile=`pwd`/8.images/2.back/dockerfile \
                    --destination=${REGISTRY_URL}/${IMAGE_TAG} \
                    --skip-tls-verify=true
                """
              }
            }
          }
        }
      }
    }
  }
}

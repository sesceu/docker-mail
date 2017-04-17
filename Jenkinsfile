pipeline {
  agent any
  environment {
    DOCKER_NAME_AMAVIS = 'sesceu/amavis'
    DOCKER_NAME_OPENDKIM = 'sesceu/opendkim'
    DOCKER_NAME_POSTGREY = 'sesceu/postgrey'
    DOCKER_NAME_POSTFIX = 'sesceu/postfix'
  }
  stages {
    stage('Docker Build') {
      steps {
        parallel(
          "amavis" : {
            dir("amavis") {
              sh "docker build -t $DOCKER_NAME_AMAVIS:${env.BUILD_NUMBER} --no-cache ."
              sh "docker tag $DOCKER_NAME_AMAVIS:${env.BUILD_NUMBER} $DOCKER_NAME_AMAVIS:latest"
            }
          },
          "opendkim" : {
            dir("opendkim") {
              sh "docker build -t $DOCKER_NAME_OPENDKIM:${env.BUILD_NUMBER} --no-cache ."
              sh "docker tag $DOCKER_NAME_OPENDKIM:${env.BUILD_NUMBER} $DOCKER_NAME_OPENDKIM:latest"
            }
          },
          "postgrey" : {
            dir("postgrey") {
              sh "docker build -t $DOCKER_NAME_POSTGREY:${env.BUILD_NUMBER} --no-cache ."
              sh "docker tag $DOCKER_NAME_POSTGREY:${env.BUILD_NUMBER} $DOCKER_NAME_POSTGREY:latest"
            }
          },
          "postfix" : {
            dir("postfix") {
              sh "docker build -t $DOCKER_NAME_POSTFIX:${env.BUILD_NUMBER} --no-cache ."
              sh "docker tag $DOCKER_NAME_POSTFIX:${env.BUILD_NUMBER} $DOCKER_NAME_POSTFIX:latest"
            }
          }
        )
      }
    }
    stage('Docker Push') {
      environment {
        DOCKER_HUB = credentials('docker-hub-credentials')
      }
      steps {
        parallel(
          "amavis" : {
            sh "docker login --username \"$DOCKER_HUB_USR\" --password \"$DOCKER_HUB_PSW\""
            sh "docker push $DOCKER_NAME_AMAVIS:${env.BUILD_NUMBER}"
            sh "docker push $DOCKER_NAME_AMAVIS:latest"
          },
          "opendkim" : {
            sh "docker login --username \"$DOCKER_HUB_USR\" --password \"$DOCKER_HUB_PSW\""
            sh "docker push $DOCKER_NAME_OPENDKIM:${env.BUILD_NUMBER}"
            sh "docker push $DOCKER_NAME_OPENDKIM:latest"
          },
          "postgrey" : {
            sh "docker login --username \"$DOCKER_HUB_USR\" --password \"$DOCKER_HUB_PSW\""
            sh "docker push $DOCKER_NAME_POSTGREY:${env.BUILD_NUMBER}"
            sh "docker push $DOCKER_NAME_POSTGREY:latest"
          },
          "postfix" : {
            sh "docker login --username \"$DOCKER_HUB_USR\" --password \"$DOCKER_HUB_PSW\""
            sh "docker push $DOCKER_NAME_POSTFIX:${env.BUILD_NUMBER}"
            sh "docker push $DOCKER_NAME_POSTFIX:latest"
          }
        )
      }
    }
  }
}

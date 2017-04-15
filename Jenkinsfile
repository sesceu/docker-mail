pipeline {
  agent any
  environment {
    DOCKER_NAME_AMAVIS = 'sesceu/amavis'
    DOCKER_NAME_OPENDKIM = 'sesceu/opendkim'
    DOCKER_NAME_POSTFIX = 'sesceu/postfix'
  }
  stages {
    stage('Docker Build') {
      steps {
        parallel(
          "amavis" : {
            dir "amavis"
            sh "docker build -t $DOCKER_NAME_AMAVIS:${env.BUILD_NUMBER} --no-cache ."
            sh "docker tag $DOCKER_NAME_AMAVIS:${env.BUILD_NUMBER} $DOCKER_NAME_AMAVIS:latest"
          },
          "opendkim" : {
            dir "opendkim"
            sh "docker build -t $DOCKER_NAME_OPENDKIM:${env.BUILD_NUMBER} --no-cache ."
            sh "docker tag $DOCKER_NAME_OPENDKIM:${env.BUILD_NUMBER} $DOCKER_NAME_OPENDKIM:latest"
          },
          "amavis" : {
            dir "amavis"
            sh "docker build -t $DOCKER_NAME_POSTFIX:${env.BUILD_NUMBER} --no-cache ."
            sh "docker tag $DOCKER_NAME_POSTFIX:${env.BUILD_NUMBER} $DOCKER_NAME_POSTFIX:latest"
          }
        )
      }
    }
    stage('Docker Push') {
      environment {
        DOCKER_HUB = credentials('docker-hub-credentials')
      }
      steps {
        sh "docker login --username \"$DOCKER_HUB_USR\" --password \"$DOCKER_HUB_PSW\""
      }
    }
  }
}

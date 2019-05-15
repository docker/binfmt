DOCKER_HUB_CREDS = [[
    $class: 'UsernamePasswordMultiBinding',
    usernameVariable: 'REGISTRY_USERNAME',
    passwordVariable: 'DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE',
    credentialsId: 'dockerbuildbot-index.docker.io',
]]

pipeline {
  agent {
      label 'linux && amd64 && ubuntu-1804 && docker'
  }

  parameters {
    booleanParam(name: "push", defaultValue: false)
  }

  options {
    timeout(time: 1, unit: 'HOURS')
    timestamps()
    ansiColor('xterm')
  }
  stages {
    stage('build') {
      steps {
        sh 'make build'
      }
    }
    stage('test') {
      steps {
        sh 'make test'
      }
    }
    stage('push') {
      when {
        beforeAgent true
        expression { params.push }
      }
      steps {
        withDockerRegistry(url: "https://index.docker.io/v1/", credentialsId: 'dockerbuildbot-index.docker.io') {
          withCredentials(DOCKER_HUB_CREDS) {
            sh 'export $DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE=$DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE && bin/linuxkit pkg push -org docker binfmt'
          }
        }
      }
    }
  }
}

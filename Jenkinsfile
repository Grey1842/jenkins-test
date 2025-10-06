pipeline {
  agent any
  environment {
    DOCKER_REGISTRY = 'grey1842' // change this
  }
  stages {
    stage('Checkout Source') {
      steps {
        git url: 'https://github.com/Grey1842/jenkins-test.git', branch: 'main' // update URL
      }
    }
    stage('Build & Push Docker Images') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'docker-hub-creds',
                                             usernameVariable: 'DOCKER_USERNAME',
                                             passwordVariable: 'DOCKER_PASSWORD')]) {
            bat "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
            bat "docker-compose build web"
            def imageTag = "${DOCKER_REGISTRY}/my-nodejs-app:${env.BUILD_NUMBER}"
            bat "docker tag my-nodejs-app:latest ${imageTag}"
            bat "docker push ${imageTag}"
          }
        }
      }
    }
    stage('Deploy Multi-Container App') {
      steps {
        script {
          bat "docker stop node-web node-mongo-db || true"
          bat "docker rm node-web node-mongo-db || true"
          bat "BUILD_NUMBER=${env.BUILD_NUMBER} docker-compose up -d --force-recreate"
        }
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}
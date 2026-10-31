pipeline {
  agent any
  environment {
    IMAGE="docker.io/hokage004/demo-app"
    TAG="${BUILD_NUMBER}" 
  }
  stages {
    stage('checkout') {
      steps {
        git branch: 'main', credentialsId: 'github-pat', poll: false, 
        url: 'https://github.com/hokage004/private-repo.git'
      }
    }
    stage('build') {
      steps {
        sh 'docker build -t "$IMAGE:$TAG" -t "$IMAGE:latest" .'
      }
    }
    stage('push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKERHUB_PWD', usernameVariable: 'DOCKERHUB_USER')]) {
        sh 'echo "$DOCKERHUB_PWD" | docker login -u "$DOCKERHUB_USER" --password-stdin'

        sh 'docker push "$IMAGE:$TAG"'
        sh 'docker push "$IMAGE:latest"'
      }
      }
    }
    stage('deploy') {
      steps {
        sh 'docker pull "$IMAGE:$TAG"'
        sh 'docker rm -f test-flask || true'
        sh 'docker run -d --name test-flask -p 5000:5000 "$IMAGE:$TAG"'
      }
    }
    stage('test') {
      steps {
        sh 'echo "Hit http://localhost:5000 to see the app."'
      }
    }
     stage('cleanup') {
      steps {
        cleanWs()
      }
    }
    
  }
}

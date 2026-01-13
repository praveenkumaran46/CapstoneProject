pipeline {
  agent any
  environment {
    IMAGE="yourdockerhub/node-devops"
  }
  stages {
    stage("Checkout") {
      steps { git branch: 'main', url: 'https://github.com/YOURNAME/YOURREPO.git' }
    }
    stage("Build") {
      steps { sh 'npm install' }
    }
    stage("Docker Build") {
      steps { sh 'docker build -t $IMAGE:$BUILD_NUMBER .' }
    }
    stage("Push Docker") {
      steps {
        withCredentials([string(credentialsId: 'docker-pass', variable: 'PASS')]) {
          sh "echo $PASS | docker login -u yourdockerhub --password-stdin"
        }
        sh "docker push $IMAGE:$BUILD_NUMBER"
      }
    }
    stage("Deploy to EC2") {
      steps {
        sshagent(['ec2-key']) {
          sh '''
          ssh -o StrictHostKeyChecking=no ubuntu@EC2_IP \
          "docker pull $IMAGE:$BUILD_NUMBER &&
           docker stop nodeapp || true &&
           docker rm nodeapp || true &&
           docker run -d -p 80:3000 --name nodeapp $IMAGE:$BUILD_NUMBER"
          '''
        }
      }
    }
  }
}

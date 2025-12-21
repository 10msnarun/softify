pipeline {
  agent any

  
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/10msnarun/softify.git'
      }
    }

    stage('Build images') {
      steps {
        sh '''
        cd backend
        docker build -t softify-api:latest .
        cd ../frontend
        docker build -t softify-frontend:latest .
        '''
      }
    }

    stage('Deploy to App VM') {
      steps {
        sshagent(credentials: ['app-ec2-ssh']) {
          sh '''
          ssh -o StrictHostKeyChecking=no ubuntu@13.235.87.236/ '
            cd /root/softify &&
            git pull origin main &&
            docker compose down &&
            docker compose up --build -d
          '
          '''
        }
      }
    }
  }
}

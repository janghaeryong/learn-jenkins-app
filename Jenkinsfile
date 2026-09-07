pipeline{
  agent any

  stages {
    stage('Build') {

      agent {
        docker {
          image 'node:18-alpine' 
          reuseNode true
        }
      }

      steps {
        sh '''
          ls -la
          node --version
          npm --version
          npm ci
          npm run build
          ls -la
          find build -type f -name "index.html"
        '''
      }
    }
    
    stage('Test'){
      steps {
         sh '''
          ls -la
          npm run test
          ls -la
        '''
      }
    }
  }
}  
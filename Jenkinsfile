pipeline{
  agent {
    docker {
      image 'node:18-alpine' 
      reuseNode true
    }
  }

  stages {
    stage('Build') {

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
          npm run test a
          ls -la
        '''
      }
    }
  }
}  
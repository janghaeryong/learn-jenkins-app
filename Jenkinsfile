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
      echo 'Test Stage'
      steps {
         sh '''
          test -f build/index.html
          npm test
        '''
      }
    }
  }
}  
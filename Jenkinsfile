pipeline {
     // 전역 에이전트를 사용하지 않음으로써 컨테이너 중첩 방지
    agent none 

    environment {
      REACT_APP_VERSION = "1.0.$BUILD_ID"
      APP_NAME = 'myjenkinsapp'
      AWS_DEFAULT_REGION = 'ap-northeast-2'
      AWS_ECS_CLUSTER = 'excellent-lion-kxzxfl'
      AWS_ECS_SERVICE_PROD = 'LearnJenkinsApp-Service-Prod'
      AWS_ECS_TD_RPOD = 'LearnJenkinsApp-TaskDefinition-Prod'
      
    }

    stages {
        stage('Build') {
            agent {
                docker {
                   image 'mcr.microsoft.com/playwright:v1.39.0-jammy' 
                   reuseNode true
                }
            }
            steps {
                sh '''
                    echo '빌드 시작..'
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Build Docker Image'){
          agent {
                docker { 
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u root --entrypoint='' -v /var/run/docker.sock:/var/run/docker.sock" 
                }
            }

            steps {
              withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                sh '''
                  yum install -y docker
                  docker build --platform linux/amd64 -t $APP_NAME:$REACT_APP_VERSION .
                '''
              }
            }
        }

        stage('Deploy to AWS') {
            agent {
                docker { 
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u root --entrypoint=''" 
                }
            }

            steps {
              withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                sh '''
                  aws --version
                  yum install jq -y
                  LATEST_TD_REVISION=$(aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json | jq '.taskDefinition.revision')
                  echo $LATEST_TD_REVISION
                  aws ecs list-clusters --region ap-northeast-2
                  aws ecs update-service --cluster $AWS_ECS_CLUSTER --service $AWS_ECS_SERVICE_PROD --task-definition $AWS_ECS_TD_RPOD:$LATEST_TD_REVISION
                  aws ecs wait services-stable --cluster $AWS_ECS_CLUSTER --service $AWS_ECS_SERVICE_PROD
                '''
              }
            }
        }
    }
}

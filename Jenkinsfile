pipeline {
    agent any
    stages {
      stage('Git Checkout Source') {
      steps {
        checkout scm
        }
      }
      stage("Docker variables") {
        steps {
        script {
        env.ECRREPOURI = "633925767990.dkr.ecr.us-east-1.amazonaws.com/node"
        env.DOCKERPUSHURL = "https://633925767990.dkr.ecr.us-east-1.amazonaws.com/node"
        env.TAG = "development" + "${BUILD_NUMBER}"
        echo "Tag : ${env.TAG}"
        env.IMAGE = "${env.ECRREPOURI}" + ":" + "${env.TAG}"
                }
            }
        }
      stage("Docker build") {
                steps {
                    sh """ 
                        pwd
                        docker build -t ${env.ECRREPOURI}:${env.TAG} .
                    """
                }
            }
      stage("Docker push to AWS ECR") {
            steps{
                script{
                     sh("eval \$(aws2 ecr get-login --no-include-email)")
                     docker.withRegistry("${env.DOCKERPUSHURL}", "ecr:us-east-1:aws-creds") {
                     docker.image("${env.IMAGE}").push()
                    }
                }
            }
        }
      stage("Docker Deploy") {
                steps {
                    sh """ 
                        sed -i 's@BUILD@'"${env.IMAGE}"'@' docker-compose.yml
                        cat docker-compose.yml
                        docker-compose up -d
                    """
                } 
            }
        }
    }

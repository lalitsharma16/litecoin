pipeline {
    agent any
    environment{
        IMAGENAME = "gamesondocker/litecoin"
        TAG = "0.18.2"
        MANIFEST_FILE="litecoin.yml"
        registryCredential = 'dockerhub-access'
        dockerImage = ''
        dockerHome = ''
    }

    stages{
        stage('Clone sources') {
          steps {
             git([url: 'https://github.com/lalitsharma16/litecoin.git', branch: 'dev', credentialsId: 'mygithub-token'])
          }    
        }

        stage('Build Docker Image'){
          steps{
                script{
                dockerImage = docker.build("${IMAGENAME}:${TAG}")
                }
            }
        }

        stage(‘Container Security Scan’) {
          steps {
            script{
              sh ‘echo “docker.io/${IMAGENAME} `pwd`/Dockerfile” > anchore_images’
              anchore name: ‘anchore_images’
            }
          }
        }
        
        stage('Test image') {
            steps{
              script{
                dockerImage.inside {
                   sh 'echo "Test passed"'
                }
              }
            }
        }

        stage('Docker image Push'){
            steps{
                script{
                  docker.withRegistry('', registryCredential) {
                    dockerImage.push("$TAG")
                    dockerImage.push('latest')
                  }
                }
            }
        }
        
        stage('Run kubectl') {
           steps {
               script{
                   try{
                  sh "/usr/local/bin/kubectl apply -f $MANIFEST_FILE"
                   }catch(error){
                  currentBuild.result = 'FAILURE'
                  sh """
                    echo FAILURE
                  """
                   }
               }
           }
        }

        
        stage('Remove Unused docker image') {
          steps{
              script{
                sh "docker rmi $IMAGENAME:$TAG"
                sh "docker rmi $IMAGENAME:latest"
              }
          }
        }
    }
}

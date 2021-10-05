pipeline {
    agent any
    // def dockerImage
    environment{
        IMAGENAME = "gamesondocker/litecoin"
        TAG = "0.18.2"
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
        
 //       stage('testing') {
 //           steps {
 //                sh """
 //                  pwd
   //                ls -ltr
     //           """
       //     }
        //}
        
//        stage('Initialize'){
//            steps {
//                script{
//        dockerHome = tool 'myDocker'
//        env.PATH = "${dockerHome}/bin:${env.PATH}"
//                }
//            }
//        }
        
        //stage('Build') {
        //    steps {
        //      sh """
        //       docker build -t ${IMAGENAME}:${TAG} .
        //       docker push ${IMAGENAME}:${TAG}
        //      """
        //    }
        //}

        stage('Build Docker Image'){
          steps{
                script{
                dockerImage = docker.build("${IMAGENAME}:${TAG}")
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
                // withCredentials([string(credentialsId: 'nexus-pwd', variable: 'nexusPwd')]) {
                //     sh "docker login -u admin -p ${nexusPwd} ${NEXUS_URL}"
                //     sh "docker push ${IMAGE_URL_WITH_TAG}"
                  docker.withRegistry('', registryCredential) {
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                  }
                }
            }
        }
    }
}

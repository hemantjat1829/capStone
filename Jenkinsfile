pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-southeast-2:CapStoneProject'
       appRegistry = "785969298449.dkr.ecr.ap-southeast-2.amazonaws.com/202051084_repo"
       capstoneRegistry = "https://785969298449.dkr.ecr.ap-southeast-2.amazonaws.com"
       cluster = "202051084_CapStoneCluster"
        service = "202051084_service"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/hemantjat1829/capStone'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'CapStoneProject', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}

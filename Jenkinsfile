pipeline {
environment {
registry = "vijayshegde/appnode"
registryCredential = 'docker'
dockerImage = ''
}
agent any
stages{
stage('Checkout'){
steps{
checkout([$class: 'GitSCM', branches: [[name: '*/main']],

     userRemoteConfigs: [[url: '']]])
}
}

stage('Initialize'){
steps {
script {
def dockerHome = tool 'myDocker'
env.PATH = "${dockerHome}/bin:${env.PATH}"
}
}
}

stage('Building our image')
{
steps
{
sh 'docker --version'
script
{
dockerImage = docker.build registry + ":$BUILD_NUMBER"
}
}
}
stage('Deploy image') {
steps {
script {
docker.withRegistry( '', registryCredential ) {
dockerImage.push()
}
}

     
      sh  'docker images'

         }

     }
      stage('Run Image'){
           steps{
               sh ''' 
               if [ $(docker ps -qf "name=appnode") ]
                then
                echo "from if block"
                docker kill appnode && docker rm appnode
                docker run -d -p 8760:8080 --name appnode "${registry}":"${BUILD_NUMBER}"
                docker ps
               else
                echo "from else block"
                docker run -d -p 8760:8080 --name appnode "${registry}":"${BUILD_NUMBER}"
                docker ps
                fi
               '''
           }
       }   
// stage('Cleaning up') {
// steps {
// sh "docker rmi $registry:$BUILD_NUMBER"
// }
// }
}
}

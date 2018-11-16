pipeline {
    agent none
    environment {
        imageName = 'yutanui/my_web_ex'
        port = 9090
    }
    
    stages {
       stage('Package') { 
          agent {label 'mgr1'}
          steps {
              sh "docker --version"
              sh "docker build -t ${imageName} ."
            withCredentials(
                [usernamePassword(credentialsId: 'docker_hub', 
                passwordVariable: 'dockerHubPassword', 
                usernameVariable: 'dockerHubUser')]) {
              sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
              sh "docker push ${imageName}"
            }
          }
       }

       stage('Deploy') { 
          agent {label 'mgr1'}
          steps {
            script {
                  try {
                    sh "docker service update --image ${env.imageName} my_web_ex"
                    sh "echo update service"
                  } catch (e){
                    sh "docker service create --name my_web_ex -p ${env.port}:80 ${env.imageName}"
                    sh "echo create service"
                  }
          }
       }
    }
}
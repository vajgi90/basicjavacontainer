node {
    stage("Git Clone"){

        git credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/vajgi90/basicjavacontainer.git'
    }
    stage('Gradle Build') {

       sh './gradlew build'

    } 
    stage("Docker build"){
        sh 'docker version'
        sh 'docker build -t vajgi90/kubespring .'
        sh 'docker image list'
    } 
    stage("Docker Login"){
        withCredentials([string(credentialsId: 'DOCKER_HUB_TOKEN', variable: 'PASSWORD')]) {
            sh 'docker login -u vajgi90 -p $PASSWORD'
        }
    }
    stage('Push') {
        sh 'docker push vajgi90/kubespring'
    }
    stage("SSH Into k8s Server") {
        def remote = [:]
        remote.name = 'k8smaster'
        remote.host = '192.168.56.2'
        remote.user = 'vagrant'
        remote.password = 'vagrant'
        remote.allowAnyHosts = true

        stage('Put deployment-service.yml onto k8smaster') {
            sshPut remote: remote, from: 'deployment-service.yaml', into: '.'
        }

        stage('Deploy spring boot') {
          sshCommand remote: remote, command: "kubectl apply -f deployment-service.yaml"
        }
    }    
    
    post {
    always {
      sh 'docker logout'
    }
  }
}

pipeline{
  agent any
  environment {
    DOCKER_USER = "theanh0906"
    DOCKER_PASS = "dockerhub-credentials"
  }
  stages{
    stage('Cleanup workspace'){
      steps{
        cleanWs()
      }
    }
    
    stage('Checkout'){
      steps{
        git branch: "develop", credentialsId: 'github-credentials', url: 'https://github.com/theanhtr/PEMS_Helm.git'
      }
    }

    stage("Push Helm Chart"){
      steps {
        script {
          def newVersion = sh(script: "cat Chart.yaml | grep version: | cut -d' ' -f2 |tr -d '\n'", returnStdout: true)
          println "Chart version is : ${newVersion}"
          def chartNameDetect = sh(script: "cat Chart.yaml | grep name: | cut -d' ' -f2 |tr -d '\n'", returnStdout: true)
          println "Chart name is : ${chartNameDetect}"

          sh """
              pwd
              helm package .
              ls
              helm registry login registry-1.docker.io -u ${DOCKER_USER} --password=${DOCKER_PASS}
              helm push ${chartNameDetect}-${newVersion}.tgz  oci://registry-1.docker.io/${DOCKER_USER}
              rm -f ${chartNameDetect}-${newVersion}.tgz
          """

          withCredentials([usernamePassword(credentialsId: ${DOCKER_PASS}, passwordVariable: 'C_PASS', usernameVariable: 'C_USER')]) {
            sh """
              pwd
              helm package .
              ls
              helm registry login registry-1.docker.io -u ${DOCKER_USER} --password=${C_PASS}
              helm push ${chartNameDetect}-${newVersion}.tgz  oci://registry-1.docker.io/${DOCKER_USER}
              rm -f ${chartNameDetect}-${newVersion}.tgz
            """
          }
          
        }
      }
    }
  }
}
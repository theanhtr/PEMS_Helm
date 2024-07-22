pipeline{
  agent any
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
            dir("/") {
                def newVersion = sh(script: "cat Chart.yaml | grep version: | cut -d' ' -f2 |tr -d '\n'", returnStdout: true)
                println "Chart version is : ${newVersion}"
                def chartNameDetect = sh(script: "cat Chart.yaml | grep name: | cut -d' ' -f2 |tr -d '\n'", returnStdout: true)
                println "Chart name is : ${chartNameDetect}"
                sh """
                    pwd
                    helm package .
                    ls
                    helm repo add chartmuseum http://localhost:8080 --force-update
                    helm cm-push ${chartNameDetect}-${newVersion}.tgz chartmuseum
                    rm -f ${chartNameDetect}-${newVersion}.tgz
                """
            }
        }
      }
    }
  }
}
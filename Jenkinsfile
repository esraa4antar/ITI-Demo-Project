pipeline {
  agent { label "slave"}
  stages {
    stage('build') {
      steps {
        script {
       
            withCredentials([usernamePassword(credentialsId: '9f6f1288-82c9-4288-8b12-f6e9f259365d', usernameVariable: 'username', passwordVariable: 'password')]) {
              sh """
        
                  docker login -u ${username} -p ${password}
                  docker build -t esraaantar/demo  .
                  docker push esraaantar/demo
              """
          }
        } 
      }
    }
    stage('deploy') {
    
      steps {
        script {

          withCredentials([file(credentialsId: 'key', variable: 'key')]){
              sh """
                  gcloud auth activate-service-account manag-service-acc@lithe-quest-368003.iam.gserviceaccount.com -- key-file ${key}
        
                """
            }
        

            withCredentials([file(credentialsId: 'esraa/kube', variable: 'KUBECONFIG')]) {
              sh """
                  kubectl apply -f deployment.yaml --kubeconfig=${KUBECONFIG}
                """
            }
        }
      }
    }
  }
}

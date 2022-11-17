pipeline {
  agent { label "slave"}
  stages {
    stage('build') {
      steps {
        script {
       
            withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'username', passwordVariable: 'password')]) {
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

            withCredentials[file(credentialsId: 'key', variable: 'key')] {
              sh """
                  gcloud auth activate-service-account manag-service-acc@lithe-quest-368003.iam.gserviceaccount.com --key-file ${key}
        
                """
            }
        }


            withCredentials([file(credentialsId: 'esraa/kube', variable: 'KUBECONFIG')]) {
              sh """
                  export BUILD_NUMBER=\$(cat ../demo-build-number.txt)
                  mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                  cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                  rm -f Deployment/deploy.yaml.tmp
                  kubectl apply -f Deployment --kubeconfig=${KUBECONFIG}
                """
            }
        }
      }
    }
  }

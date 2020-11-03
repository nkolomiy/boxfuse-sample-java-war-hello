void init_ssh(String ssh_key)
{
    echo "Configure ssh"
    sh """
        mkdir -p ~/.ssh
        cat ${ssh_key} > ~/.ssh/id_rsa
        chmod 600 ~/.ssh/id_rsa
        echo "Host *" > ~/.ssh/config
        echo "    StrictHostKeyChecking no" >>  ~/.ssh/config
    """
}

pipeline {
  agent {
      label 'docker'
  }
  stages {
    stage('Building package') {
      steps{
        script {
          sh "mvn package"
        }
      }
    }
    stage('Deploy package') {
      steps{
        script {        
        withCredentials([usernamePassword(credentialsId: 'deployer', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASSWORD')]) {
        sh """
          CURL_RESPONSE=\$(curl -v -u $TOMCAT_USER:$TOMCAT_PASSWORD -T target/*.war "http://35.228.171.146:80/manager/text/deploy?path=/helloworld&update=true")    
          if [[ \$CURL_RESPONSE == *"FAIL"* ]]; then
            echo "war deployment failed"
            exit 1
          else
            echo "war deployed successfully "
            exit 0
          fi
        """
          }
        }
      }
    }
  }
}


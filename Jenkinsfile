pipeline {
    agent any
    
    tools  {
        maven "my-maven"
    }


    stages {

        // Start SonarQube Server
        stage("Start SonarQube Server"){
            steps{
                echo "====++++  Start SonarQube Server ++++===="
                
                    //   withCredentials([usernameColonPassword(credentialsId: 'user-password-vagrant', variable: 'USERPASS')]) {
                    sh '''
                      set +x
                      sudo docker run -d --name my-sonarqube -p 9000:9000 sonarqube:lts
                    '''
                   //    }
                
            }
        }

      // Clone from Git
        stage("Clone App from Git"){
            steps{
                echo "====++++  Clone App from Git ++++===="
                git branch:"master", url: "https://github.com/mromdhani/challenge-09-ci-cd-jenkins-ansible.git"
            }          
        }
        // Build and Unit Test (Maven/JUnit)
        stage("Build and Package"){
            steps{
                echo "====++++  Build and Unit Test (Maven/JUnit) ++++===="
                sh "mvn clean package"
            }           
        }        
         // Deploiement du WAR sur le server-staging avec Ansible
        stage("Deploy WAR on staging using Ansible"){
            steps{
                
                echo "====++++  Deploy WAR on staging using Ansible ++++===="
      
                ansiblePlaybook(credentialsId: 'ssh-on-server-staging', 
                                  inventory:  "$WORKSPACE/ansible/hosts", 
                                  playbook: 'ansible/playbook-deploy-staging.yaml')          
            } 
        }


         // Stop SonarQube Server
        stage("Stop SonarQube Server"){
            steps{
                echo "====++++  Stop SonarQube Server ++++===="
                sh "sudo docker stop my-sonarqube && sudo docker rm my-sonarqube" 
            }          
        }        
    }
}
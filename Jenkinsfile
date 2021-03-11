pipeline {
    agent any
    
    tools  {
        maven "my-maven"
    }


    stages {
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
                                  inventory:  $WORKSPACE/ansible/hosts, 
                                  playbook: 'ansible/playbook-deploy-staging.yaml')          
            } 
        }        
    }
}
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
                
                sh " sudo docker run -d --add-host=host.docker.internal:172.17.0.1 --name my-sonarqube -p 9000:9000 sonarqube:lts"                    
                
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

        // Static Code Analysis (SonarQube)
        stage("Static Code Analysis (SonarQube)"){
            steps{
                echo "====++++  Static Code Analysis (SonarQube) ++++===="                
          //      withSonarQubeEnv('my_sonarqube_in_docker') {  
                sh "mvn clean package clean package -Dsurefire.skip=true sonar:sonar -Dsonar.host.url=http://172.17.0.1:9000   -Dsonar.projectName=challenge-09-ci-cd-jenkins-ansible -Dsonar.projectVersion=$BUILD_NUMBER";
             
           //     }  
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
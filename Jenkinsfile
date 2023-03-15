pipeline {
    
    agent {
        
        label "mavenslave"
    } 

    stages {
        stage('SCM') {
            steps {
                    git 'https://github.com/ppraveen11/jenkins-docker-maven-java-webapp.git'
            }
        }
        
        
        stage("build package by maven"){
                   steps{
                      sh 'mvn clean package'
                   }
                  } 
        
        stage("build docker image"){
                    
                      steps{ 
                          
                          sh "sudo docker build  -t   mondisaidurga01/javaweb:${BUILD_TAG}  ."
                          
                      }
        }
        
        stage("push image to docker hub"){
                    
           steps{ 
                          
           withCredentials([string(credentialsId: 'SAI-DOCKERHUB-PSWD', variable: 'docker_hub_psswd_var2')]) {
    // some block

                       sh "sudo docker login  -u  mondisaidurga01  -p  $docker_hub_psswd_var2" 

               }
                          
           sh "sudo docker push   mondisaidurga01/javaweb:${BUILD_TAG}"
              
                           
   }
        }
        
            
        stage("Deploying WEBAPP in dev env and  QAT/TEST  "){ 
            steps{ 
                
                sshagent(['FORQAT']) {
    // some block
                 sh 'ssh  -o  StrictHostKeyChecking=no ec2-user@13.232.63.60' 
                 
                 sh 'sudo docker rm -f javawebapp || true'
                  
                 sh "sudo docker run -d  -p  1234:8080  --name  javawebapp    mondisaidurga01/javaweb:${BUILD_TAG}" 
                    
                 //sh 'curl --silent  http://3.110.85.253:1234/java-web-app/ | grep India'
                        
                  echo "hello its QAT"  
                
                 
                }
        }   }
            
           
                
             
                
          stage('approved') {
            steps {
                
            
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }
            
                
            }
        }
        }
          stage("Deploying WEBAPP in prod env "){ 
            steps{ 
                
                sshagent(['FORQAT']) {
              
                    sh 'ssh  -o  StrictHostKeyChecking=no ec2-user@13.234.114.208' 
                    sh 'sudo docker rm -f javawebapp || true'
                    sh "sudo docker run -d  -p  1234:8080  --name  javawebapp    mondisaidurga01/javaweb:${BUILD_TAG}"
                    
                   
                
                 
                }
        }   }
      


        }
                 
    } 

pipeline{
   agent {label 'worker'}
   
    environment {
    DOCKER_HUB_CREDS = credentials('dockerhub')
   }
  stages{
     
    stage("checkout voting app"){
      
        steps {
          checkout scm
        }
       
         
      
    }
    stage("Build vote app"){
      steps{
          dir("${env.WORKSPACE}/vote"){
           sh "pwd"
           sh "docker login -u $DOCKER_HUB_CREDS_USR -p $DOCKER_HUB_CREDS_PSW" 
           sh "docker build -t $DOCKER_HUB_CREDS_USR/voteapp:latest ."
           
             
           
           sh "docker push mohit1412/voteapp:latest"
       }
       
         
      }
    }
  }
    
    
  
    post { 
        always { 
            cleanWs()
        }
    }
}

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
      stage("Deploy app using compose"){
      steps{
       script {
                    SSH =  'ssh -tt -i /home/ubuntu/demo.pem ubuntu@172.31.1.49'
                    sh "$SSH 'cd /home/ubuntu/workspace/voteapp; sed -i 's+build: ./vote+image: mohit1412/voteapp:latest+g' docker-compose.yml; COMPOSE_HTTP_TIMEOUT=200 docker-compose -f docker-compose.yml up -d'"}
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

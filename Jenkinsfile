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
      stage("Deploy app using compose") {
         agent { label 'master' }
      steps{
       script {
                    SSH =  'ssh -tt  -o StrictHostKeyChecking=no -i /home/ubuntu/demo.pem ubuntu@172.31.1.49'
                    sh "$SSH 'cd /home/ubuntu/workspace/voteapp; ls;sed -i "s+build: ./vote+image: mohit1412/voteapp:latest" docker-compose.yml;  docker-compose -f docker-compose.yml up -d;'"
              }
           }
         }
  }
    
    
  
   
}

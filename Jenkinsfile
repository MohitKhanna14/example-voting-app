pipeline {
    agent {
      label 'worker'
    }

    options {
            buildDiscarder(logRotator(numToKeepStr: '10'))
            disableConcurrentBuilds()
            timeout(time: 1, unit: 'HOURS')
    }
    environment {
            AWS_DEFAULT_REGION = 'us-east-1'
            SERVICE_NAME = 'voteservice'
            TASK_FAMILY = 'vote-fargate1'
            ECS_CLUSTER = 'vote-app'
    }
    stages {
      stage('Git Checkout') {
        steps {
          checkout scm
        }
      }
        stage('Build Docker Image') {
            steps {
                slackSend color: "#439FE0", message: "Build Started - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
                sh "eval \$(aws ecr get-login --no-include-email --region us-east-1) && sleep 2"
                sh "cd vote && docker build . -t 462273782981.dkr.ecr.us-east-1.amazonaws.com/vote:\${BUILD_NUMBER}"
                sh "docker push 462273782981.dkr.ecr.us-east-1.amazonaws.com/vote:\${BUILD_NUMBER}"
            }
        }
        stage('Deploy in ECS') {
            steps {

                script {
                    sh'''
                    ECR_IMAGE="462273782981.dkr.ecr.us-east-1.amazonaws.com/vote:${BUILD_NUMBER}"
                    TASK_DEFINITION=$(aws ecs describe-task-definition --task-definition "$TASK_FAMILY" --region "$AWS_DEFAULT_REGION")
                    NEW_TASK_DEFINTIION=$(echo $TASK_DEFINITION | jq --arg IMAGE "$ECR_IMAGE" '.taskDefinition | .containerDefinitions[0].image = $IMAGE | del(.taskDefinitionArn) | del(.revision) | del(.status) | del(.requiresAttributes) | del(.compatibilities)')
                    NEW_TASK_INFO=$(aws ecs register-task-definition --region "$AWS_DEFAULT_REGION" --cli-input-json "$NEW_TASK_DEFINTIION")
                    NEW_REVISION=$(echo $NEW_TASK_INFO | jq '.taskDefinition.revision')
                    aws ecs update-service --cluster ${ECS_CLUSTER} \
                                        --service ${SERVICE_NAME} \
                                        --task-definition ${TASK_FAMILY}:${NEW_REVISION}'''
                }
            }
        }
    }

    post {
        always {
            deleteDir()
            sh "docker rmi 462273782981.dkr.ecr.us-east-1.amazonaws.com/vote:\${BUILD_NUMBER}"
            }
      failure {
        slackSend failOnError:true message: "Build failed  - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
    }
        
        }
}

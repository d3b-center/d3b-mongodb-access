#!groovy
properties([
    disableConcurrentBuilds(),
    pipelineTriggers([[$class:"GitHubPushTrigger"]])
])
pipeline {
  agent { label 'terraform-testing' }
  stages{
    stage('Get Code') {
      steps {
          deleteDir()
          checkout scm
      }
    }
   stage("Promotion aws-infra-docuementdb-nonprd-access to NON-PRD Service Environment") {
     when {
            expression {
              return env.BRANCH_NAME == 'master';
            }
          }
     steps {
                                script {
                                        env.DEPLOY_TO_DEV = input message: 'User input required',
                                                        submitter: 'lubneuskia,heatha,blackdenc',
                                                        parameters: [choice(name: 'aws-infra-docuementdb-nonprd-access: Deploy to DEV Environment', choices: 'no\nyes', description: 'Choose "yes" if you want to deploy the SERVICE environment')]
                                }
    }
   }
   stage('Deploy aws-infra-documentdb-nonprd to SERVICE') {
     when {
            environment name: 'DEPLOY_TO_DEV', value: 'yes'
            expression {
              return env.BRANCH_NAME == 'master';
            }
          }
     steps {
       slackSend (color: '#FFFF00', message: "aws-kf-postgres-access:sweat_smile:Starting to grant users to non-PRD: Branch '${env.BRANCH} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
       sh '''
       pip3 install pymongo
       aws s3 cp s3://kf-538745987955-us-east-1-service-secrets/aws-documentdb-cluster-nonprd/database.env ./
       export $(cat database.env | xargs) 
       ansible-playbook playbook/site.yml --extra-vars="apply_to_database="aws-infra-documentdb-nonprd" syslevel='service' create_users='true'"
       '''
       slackSend (color: '#00FF00', message: "aws-infra-docuementdb-nonprd-access:smile: Finished Creating Users in NON-PRD :Branch '${env.BRANCH} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
     }
     post {
       failure {
         slackSend (color: '#ff0000', message: "aws-infra-docuementdb-nonprd-access:frowning: Creating Users in NON-PRD Failed: Branch '${env.BRANCH} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
       }
     }
   }
}
}

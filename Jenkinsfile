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
   }
   stage('Deploy aws-infra-documentdb-nonprd to SERVICE') {
     when {
            expression {
              return env.BRANCH_NAME == 'master';
            }
          }
     steps {
       slackSend (color: '#FFFF00', message: "aws-kf-postgres-access:sweat_smile:Starting to grant users to non-PRD: Branch '${env.BRANCH} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
       sh '''
       aws s3 cp s3://kf-538745987955-us-east-1-service-secrets/aws-documentdb-cluster-nonprd/database.env ./
       set +x
       export $(cat database.env | xargs) 
       rm -rf database.env
       set -x
       ansible-playbook playbook/site.yml --extra-vars="apply_to_database="aws-infra-documentdb-nonprd" syslevel='service' create_users='true' type_of_db='docdb'"
       '''
       slackSend (color: '#00FF00', message: "aws-infra-docuementdb-nonprd-access:smile: Finished Creating Users in NON-PRD :Branch '${env.BRANCH} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
     }
     post {
       failure {
         slackSend (color: '#ff0000', message: "aws-infra-docuementdb-nonprd-access:frowning: Creating Users in NON-PRD Failed: Branch '${env.BRANCH} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
       }
     }
   }
   stage('Deploy kf-api-persona-mongodb to PRD') {
     when {
            expression {
              return env.BRANCH_NAME == 'master';
            }
          }
     steps {
       slackSend (color: '#FFFF00', message: "aws-kf-postgres-access:sweat_smile:Starting to grant users to non-PRD: Branch '${env.BRANCH} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
       sh '''
       aws s3 cp s3://kf-538745987955-us-east-1-prd-secrets/kf-api-persona/db-admin.env ./
       set +x
       export $(cat db-admin.env | xargs)
       rm -rf db-admin.env
       set -x
       ansible-playbook playbook/site.yml --extra-vars="apply_to_database='kf-api-persona' syslevel='prd' create_users='true' type_of_db='mongodb'"
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

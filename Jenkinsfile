//Test1
//Test2
def COMMIT
def BRANCH_NAME
def GIT_BRANCH
pipeline
{
 agent any
 environment
 {
    //  AWS_ACCOUNT_ID="470022230688"             
    //  AWS_DEFAULT_REGION="ap-south-1" 
    //  IMAGE_REPO_NAME="jenkins-java"
    //  REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
     // DOCKERHUB_CREDENTIALS=credentials('docker')
 }
 tools
 {
      maven 'maven'
 }   

 options 
 {
  buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '4', daysToKeepStr: '', numToKeepStr: '4')
  timestamps()
}
 stages
 {
     stage('Code checkout')
     {
         steps
         {
             script
             {
                 checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/poornima4824/java-complete-project.git']]])
                 COMMIT = sh (script: "git rev-parse --short=10 HEAD", returnStdout: true).trim()  
                 COMMIT_TAG = sh (script: "git tag --contains | head -1", returnStdout: true).trim() 
            }
             
         }
     }
     stage('Build')
     {   
         steps
         {
             sh "mvn clean package"
         }
     }
     stage('Execute Sonarqube Report')
     {
         steps
         {
            withSonarQubeEnv('sonar') 
             {
                sh "mvn sonar:sonar"
             }  
         }
     }
     stage('Quality Gate Check')
     {
         steps
         {
             timeout(time: 1, unit: 'HOURS') 
             {
                waitForQualityGate abortPipeline: true
            }
         }
     }
     

 }
}

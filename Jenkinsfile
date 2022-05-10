def COMMIT
def BRANCH_NAME
def GIT_BRANCH
pipeline
{
 agent any
 environment {
     jmeter="/root/apache-jmeter-5.4.3/bin"
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
stages {
     stage('Code checkout')  {
         steps {
            script
             {
                 checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/poornima4824/java-complete-project.git']]])
                 COMMIT = sh (script: "git rev-parse --short=10 HEAD", returnStdout: true).trim()  
                 COMMIT_TAG = sh (script: "git tag --contains | head -1", returnStdout: true).trim() 
            }
             
         }
     }
    stage('Build') {   
         steps {
             sh "mvn clean package"
         }
     }
    stage('Jmter test') {
         steps {
               sh """#!/bin/bash
                 sudo -S wget https://dlcdn.apache.org//jmeter/binaries/apache-jmeter-5.4.3.zip
                 sudo -S unzip apache-jmeter.zip
                 sudo -S cd apache-jmeter/bin
                 sudo -S ./jmeter 
                  """
             
                 //sh "jmeter -Jjmeter.save.saveservice.output_format=xml -n -t src/main/jmeter/Testing Diaries.jmx -l src/main/resources/JMeter.jtl"
              //sh "mvn clean verify"
                   
         }
     }
    stage('Execute Sonarqube Report') {
         steps
         {
            withSonarQubeEnv('sonar') 
             {
                sh "mvn sonar:sonar"
             }  
         }
     }
    stage('Quality Gate Check') {
         steps {
             timeout(time: 1, unit: 'HOURS') 
             {
                waitForQualityGate abortPipeline: true
            }
         }
     }

     

    }
}

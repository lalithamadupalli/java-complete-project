def COMMIT
def BRANCH_NAME
def GIT_BRANCH
pipeline
{
 agent any
 environment {
     jmeter="/opt/jmeter/jmeter/bin"
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
    stage('Jmeter test') {
         steps {
              sh "/opt/jmeter/bin/jmeter -Jjmeter.save.saveservice.output_format=xml -n -t src/main/jmeter/Testing.jmx -l src/main/jmeter/JMeter.jtl -e -o src/main/jmeter/report/output"
             // sh "/opt/jmeter/bin/jmeter.sh -Jjmeter.save.saveservice.output_format=xml -Jjmeter.save.saveservice.output_format=csv -n -t src/main/jmeter/Testing.jmx -l src/main/jmeter/JMeter.jtl"
            // sh "jmeter -Jjmeter.save.saveservice.output_format=xml -n -t src/main/jmeter/Testing Diaries.jmx -l src/main/jmeter/JMeter.jtl"
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

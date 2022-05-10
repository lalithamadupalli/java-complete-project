def COMMIT
def BRANCH_NAME
def GIT_BRANCH
pipeline
{
 agent any
 environments {
     J2_HOME="/root/apache-jmeter-5.4.3/bin"
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
    stage('intialize') {
      steps {
        sh '''
        echo "PATH= ${PATH}"
        echo "J2_HOME = ${J2_HOME}"
        '''
      }
    }
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
            sh "pwd"
                  dir("apache-jmeter-5.4.3 ")
                     sh "pwd"
                     sh "jmeter -Jjmeter.save.saveservice.output_format=xml -n -t src/main/jmeter/Testing Diaries.jmx -l src/main/resources/JMeter.jtl"
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

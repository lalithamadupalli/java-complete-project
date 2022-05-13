def COMMIT
def BRANCH_NAME
def GIT_BRANCH
pipeline
{
 agent any
 environment {
     jmeter="/opt/jmeter/bin"
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
                 //step([$class: 'WsCleanup']
                 checkout([$class: 'WsCleanup', $class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/poornima4824/java-complete-project.git']]])
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
               sh "/opt/jmeter/bin/jmeter.sh -Jjmeter.save.saveservice.output_format=xml -n -t src/main/jmeter/Testing.jmx -p src/main/jmeter//PetStore_LoadTest.properties -JTOTAL_THREADS=2 -JTEST_DURATION=60 -l MyRun1.jtl"
               step([$class: 'ArtifactArchiver', artifacts: '**/*.jtl'])
              //sh "/opt/jmeter/bin/jmeter -Jjmeter.save.saveservice.output_format=xml -n -t src/main/jmeter/Testing.jmx -l src/main/jmeter/JMeter.jtl -e -o src/main/jmeter/report/output"
              //sh "/opt/jmeter/bin/jmeter.sh -Jjmeter.save.saveservice.output_format=html -Jjmeter.save.saveservice.output_format=xml -n -t src/main/jmeter/Testing.jmx -l src/main/jmeter/MyRun1.jtl"
             step([$class: 'ArtifactArchiver', artifacts: '**/*.jtl,**/*.html'])
             //sh "mvn clean verify"
                   
         }
     }
    stage('Publish Report') {
            steps {
            
                perfReport filterRegex: '', sourceDataFiles: '**/*.jtl,**/*.html'
            
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
// post {
//       success {
//            sh "echo 'Send mail on success'"
//            mail to:"naga.poornima22@gmail.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Build is success."
//               }
//        failure {
//             sh "echo 'Send mail on failure'"
//              mail to:"naga.poornima22@gmail.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Build is failed ."
//               }
//            }
 post
 {
     success
     {
        slackSend channel: 'build-notifications',color: 'good', message: "started  JOB : ${env.JOB_NAME}  with BUILD NUMBER : ${env.BUILD_NUMBER}  BUILD_STATUS: - ${currentBuild.currentResult} To view the dashboard (<${env.BUILD_URL}|Open>)"
        emailext attachLog: true, body: '''BUILD IS SUCCESSFULL - $PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS: Check console output at $BUILD_URL to view the results. /n
 
         Regards,
         Team
 
     ''', compressLog: true, replyTo: 'naga.poornima22@gmail.com', 
        subject: '$PROJECT_NAME - $BUILD_NUMBER - $BUILD_STATUS', to: 'naga.poornima22@gmail.com'
     }
     failure
     {
         slackSend channel: 'build-notifications',color: 'danger', message: "started  JOB : ${env.JOB_NAME}  with BUILD NUMBER : ${env.BUILD_NUMBER}  BUILD_STATUS: - ${currentBuild.currentResult} To view the dashboard (<${env.BUILD_URL}|Open>)"
         emailext attachLog: true, body: '''BUILD IS FAILED - $PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:
 
         Check console output at $BUILD_URL to view the results. 
        Regards,
        Team
         ''', compressLog: true, replyTo: 'naga.poornima22@gmail.com', 
         subject: '$PROJECT_NAME - $BUILD_NUMBER - $BUILD_STATUS', to: 'naga.poornima22@gmail.com'
      }
  }
}

node{

def mavenHome = tool name: 'Maven 3.9.6'

echo "Job Name is: ${env.JOB_NAME}"
echo "Node Name is: ${env.NODE_NAME}"
echo "Jenkins Home is: ${env.JENKINS_NAME}"
echo "Jenkins URL: ${env.JENKINS_URL}"


properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '4', daysToKeepStr: '', numToKeepStr: '4')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])


try{
stage('CheckOutCode'){
   sendSlackNotifications("STARTED")
git branch: 'development', credentialsId: '85bf18b5-e6f6-47cc-a2d4-e3ae89d10dc9', url: 'https://github.com/KapielG/maven-web-application.git'
}
/*
stage('Build'){
sh "${mavenHome}/bin/mvn clean package"
}

stage('ExecuteSonarQubeReport'){
sh "${mavenHome}/bin/mvn clean sonar:sonar"
}

stage('UploadArtifactIntoNexus'){
sh "${mavenHome}/bin/mvn clean deploy"
}

stage('DeployAppIntoTomcatServer'){
sshagent(['c11b07a0-dc0a-4aa8-847f-d366a9eac185']) {
   sh "scp -o  StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.43.201:/opt/apache-tomcat-9.0.84/webapps/"
}
}
*/
}
catch(e){
currentBuild.result = "FAILURE"
    throw e
}
finally{
sendSlackNotifications(currentBuild.result)
}
}//node closing


def sendSlackNotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    colorName = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    colorName = 'GREEN'
    colorCode = '#00FF00'
  } else {
    colorName = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}

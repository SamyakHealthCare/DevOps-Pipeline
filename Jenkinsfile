node {
    
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])

echo "Build number is: ${env.BUILD_NUMBER}"
    
def mavenHome = tool name:"maven-3.6.3"
     
stage('CheckoutCode')
{
    git branch: 'development', credentialsId: '81879b4e-4dcf-4d5b-a555-7f27b3c82c43', url: 'https://github.com/SamyakHealthCare/maven-web-application-1.git'
    
}

stage('Build')
{
  sh "${mavenHome}/bin/mvn clean package" 
}

stage('Execute SonarQube Report')
{
  sh "${mavenHome}/bin/mvn sonar:sonar"
}

stage('Upload Artifact into Nexus')
{
  sh "${mavenHome}/bin/mvn deploy"
}


stage('Deploy App into Tomcat Server')
{
 sshagent(['07e7792d-37e0-4924-b3b7-06239b078114'])
{
  sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.233.86.159:/opt/apache-tomcat-9.0.38/webapps/"
}
}
 
 stage('Email Notification')
 {
  mail bcc: '', body: '''Build is over...


Regards,
Samyak.''', cc: '', from: '', replyTo: '', subject: 'Build is over...${env.BUILD_NUMBER}', to: 'satysamyak@gmail.com'
 }


}

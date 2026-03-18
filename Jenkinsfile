//This is my first scripted-way pipeline

properties([pipelineTriggers([cron('* * * * * ')])])
node
{
 def mavenHome=tool name: "maven-3.9.14"
 stage('Git checkout')
  {
    git branch: 'dev', url: 'https://github.com/Rammajjari/maven-webapplication-project-kkfunda.git'

}
 stage('compile')
{
 sh "${mavenHome}/bin/mvn compile"
}
stage('maven Build')
{
 sh "${mavenHome}/bin/mvn clean package"
}
stage('SonarQube Report')
{
 sh "${mavenHome}/bin/mvn sonar:sonar"
}
stage ('Nexus Artifact')
{

sh "${mavenHome}/bin/mvn deploy"

}

stage('Deploy to Tomcat') 
{
    withCredentials([usernamePassword(
        credentialsId: 'tomcat-cread-1',
        usernameVariable: 'USER',
        passwordVariable: 'PASS'
    )]) {
        sh """
        curl -u $USER:$PASS \
        --upload-file target/maven-web-application.war \
        "http://52.66.199.193:8080/manager/text/deploy?path=/maven-web-application&update=true"
        """
    }}
}

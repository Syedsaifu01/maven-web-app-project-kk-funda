pipeline {
    agent any
    tools {
        maven 'maven 3.9.9'
    }
    stages{
        stage('checkout'){
            steps{
                git branch: 'development', credentialsId: 'cde15b40-9724-47ff-8cdb-19ac56606910', url: 'https://github.com/Syedsaifu01/maven-web-app-project-kk-funda.git'
            }
        }
        stage('build'){
            steps{
                 sh 'mvn clean package'
            }
        }
        stage('sonar'){
            steps{
                sh 'mvn sonar:sonar'
            }   
        }
        stage('upload artifact'){
            steps{
                sh 'mvn deploy'
            }
        }
        stage('deploy'){
            steps{
                sshagent(['55c262dd-48d4-4b24-891e-3c00a309ce81']) {
                     sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.201.90.103:/opt/apache-tomcat-9.0.93/webapps/"

                }
        }
        }
    }//stages ending
    post {
  success {
    notifyBuild(currentBuild.result)
  }
  failure {
  notifyBuild(currentBuild.result)
  }
}

}//pipeline ending
def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'
  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"
  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }
  // Send notifications
  slackSend (color: colorCode, message: summary)
}

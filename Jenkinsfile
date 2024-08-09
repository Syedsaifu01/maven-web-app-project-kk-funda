pipeline {
    agent any
    tools {
        maven 'maven'    
    }
    triggers {
        pollSCM '* * * * *'
    }
    options {
        timestamps()
        authorizationMatrix([user(name: 'john', permissions: ['Credentials/Update','Credentials/View','Job/Build','Job/Cancel','Job/Delete','Job/Discover','Job/Move','Job/Read','Job/Workspace'])])
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'development', credentialsId: '8487cd1c-004d-4db2-9ef5-9386d3641418', url: 'https://github.com/KandlaguntaVenkataSivaNiranjanReddy/maven-web-app-project-kk-funda.git'
            }    
        }
        stage('Build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('SonarQube Report') {
            steps {
                sh "mvn sonar:sonar"
            }
        } 
        stage('nexus') {
            steps {
                sh "mvn clean deploy"
            }
        }
        stage('deploy to tomcat') {
            steps {
                sshagent(['8cde9fd2-eccc-4abf-8304-260130705f45']) {
                    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.60.69.218:/opt/apache-tomcat-9.0.91/webapps/"
                }
            }
        }
    }
    post {
        success {
            notifyBuild(currentBuild.result)
        }
        failure {
            notifyBuild(currentBuild.result)
        }
    }
}

def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus =  buildStatus ?: 'SUCCESS'
    def colorName = 'RED'
    def colorCode = '#FF0000'
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"
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
    slackSend (color: colorCode, message: summary,channel: '#jio-development')
}

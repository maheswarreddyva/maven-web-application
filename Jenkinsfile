node {
    try {
        notifyBuild('STARTED')

        echo "Branch name: ${env.BRANCH_NAME}"
	echo "Job Name: ${env.JOB_NAME}"
	echo "Build number: ${BUILD_NUMBER}"
	echo "Job url: ${JOB_URL}"

	properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
	
	def mavenHome = tool name: 'maven3.8.6'

	stage('git_checkout_code'){
	git branch: 'dev', credentialsId: 'e0162146-458b-46fc-b154-d4bc60ecd3c6', url: 'https://github.com/mahesh-ghe/maven-web-application.git'
	}
	stage('build'){
	sh "${mavenHome}/bin/mvn clean package"
	}
	/*
	stage('sonar_report'){
	sh "${mavenHome}/bin/mvn clean sonar:sonar"
	}
	stage('deploy_artifacts'){
	sh "${mavenHome}/bin/mvn clean deploy"
	}
	stage('deploy_to_tomcat'){
	sshagent(['4e603ae0-8e40-4518-a740-0621de9768a2']) {
		sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.32.189:/opt/apache-tomcat-9.0.65/webapps"
	}
	}
	*/
    } 
	catch (e) {
		// If there was an exception thrown, the build failed
		currentBuild.result = "FAILED"
		throw e
	} 
	finally {
		// Success or failure, always send notifications
		notifyBuild(currentBuild.result)
	}
}

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}

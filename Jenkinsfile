node{
    
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


}

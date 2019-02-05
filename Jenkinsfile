// Powered by Infostretch 

//timestamps {

//node () {

//	stage ('mvn-demo - Checkout') {
// 	 checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'kapilGit', url: 'https://github.com/kapilkaush/simple-java-maven-app.git']]]) 
//	}
//	stage ('mvn-demo - Build') {
 	
// Unable to convert a build step referring to "org.jfrog.hudson.maven3.ArtifactoryMaven3Configurator". Please verify and convert manually if required.
// Unable to convert a build step referring to "org.jfrog.hudson.maven3.Maven3Builder". Please verify and convert manually if required. 
//	}
//}
//}

pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}

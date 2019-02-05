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

isPullRequest = "${env.BRANCH_NAME}".startsWith("PR")
MVN_GROUPID = ''
MVN_VERSION = ''
MVN_ARTIFACTID = ''
tokens = "${env.JOB_NAME}".tokenize('/')
repoOrg = tokens[0]
ARTIFACTORY_BASE = 'http://localhost:8081/artifactory'
artifactory_build_link = ''
artifactory_repo_link = ''

pipeline {
    agent any
    tools {
        maven 'maven' 
    }
    
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
	stage ('Artifactory') {

			when {

				expression {isPullRequest == false}

			}

			steps {
				rtMavenResolver (
			    		id: 'resolver-unique-id',
    					serverId: 'sagoon-art1',
    					releaseRepo: 'libs-release",
    					snapshotRepo: 'libs-snapshot"
				)  
 
				rtMavenDeployer (
    					id: 'deployer-unique-id',
    					serverId: 'sagoon-art1',
    					releaseRepo: 'libs-release-local',
    					snapshotRepo: "libs-snapshot-local"
				)
				rtMavenRun (
				// Tool name from Jenkins configuration.
    					tool: maven,
    					pom: 'pom.xml',
    					goals: 'clean install',
    					// Maven options.
    					//opts: '-Xms1024m -Xmx4096m',
    					resolverId: 'resolver-unique-id'
    					deployerId: 'deployer-unique-id',
				)	
				rtPublishBuildInfo (
    					serverId: "Artifactory-1"
				)

            }
	}
    }
}

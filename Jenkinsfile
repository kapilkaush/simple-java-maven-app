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

MVN_GROUPID = ''
MVN_VERSION = ''
MVN_ARTIFACTID = ''
tokens = "${env.JOB_NAME}".tokenize('/')
repoOrg = tokens[0]
ARTIFACTORY_BASE = 'http://localhost:8081/artifactory'

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
	stage ('Artifactory & Xray') {

			when {

				expression {isPullRequest == false}

			}

			steps {

				script {

					//using 'artifacts-swn01' instead of 'artifacts' until xray problem is fixed for all nodes

					def server = Artifactory.server('sagoon-art1')

					def rtMaven = Artifactory.newMavenBuild()

					def buildInfo = Artifactory.newBuildInfo()

					buildInfo.env.capture = true

					rtMaven.resolver server: server, releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot'

					rtMaven.deployer server: server, releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot'

					rtMaven.run pom: 'pom.xml', goals: 'install -DskipTests', buildInfo: buildInfo

					server.publishBuildInfo buildInfo

					artifactory_build_link = "<li><a href='${ARTIFACTORY_BASE}/webapp/#/builds/${repoOrg}%20::%20${MVN_ARTIFACTID}%20::%20${env.BRANCH_NAME}/${env.BUILD_NUMBER}'>Artifactory Build Info</a></li>"

					if (MVN_VERSION.contains('SNAPSHOT')) {

						MVN_REPO = 'libs-snapshot'

					} else {

						MVN_REPO = 'libs-release'

					}

					REPO_SUB_DIR = MVN_GROUPID.replaceAll(/\./,'/')

					artifactory_repo_link = "<li><a href='${ARTIFACTORY_BASE}/${MVN_REPO}/${REPO_SUB_DIR}/${MVN_ARTIFACTID}/${MVN_VERSION}'>Artifactory List View</a></li>"

		}
            }
	}
    }
}

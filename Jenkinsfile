pipeline {

	environment {
		registry = "https://cloud.docker.com/repository/docker/michaelkst/simple-java-maven-app"
		registryCredential = 'dockerhub'
		dockerImage = ''
	}
    agent none 
	
    options {
        skipStagesAfterUnstable()
    }
    stages {
		stage('Initialize'){
			steps{
				def dockerHome = tool 'jenkinsDocker'
				env.PATH = "${dockerHome}/bin:${env.PATH}"
			}
		}
        stage('Build') {
			agent {
				docker {
					image 'maven:3-alpine'
					args '-v $HOME/.m2:/root/.m2'
				}
			}
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
			agent {
				docker {
					image 'maven:3-alpine'
					args '-v $HOME/.m2:/root/.m2'
				}
			}
			steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Building image and publish') {
		
			agent {
                docker { image 'node:7-alpine'}
            }
			steps {
				script {
					dockerImage = docker.build registry + ":$BUILD_NUMBER"
			
					docker.withRegistry( '', registryCredential ) {
						dockerImage.push()
					}	
				}
			}
		}
	}
}
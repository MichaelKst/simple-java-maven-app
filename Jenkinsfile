pipeline {

	agent any
	
    options {
        skipStagesAfterUnstable()
    }
    stages {
		stage('Initialize'){
			steps{
				sh 'docker -v'
			}
		}
        stage('Build') {
		    agent {
				docker {
					image 'maven:3-alpine'
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
				docker{
					image 'node:7-alpine'
				}
			}
			steps {
				script {
					dockerImage = docker.build "simple-java-maven-app" + ":$BUILD_NUMBER"
					docker.withRegistry( "https://cloud.docker.com/repository/docker/michaelkst/simple-java-maven-app", 'dockerhub' ) {
							dockerImage.push()
					}	
				}
			}
		}
	}
}
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
		stage('Building image') {
			steps {
				script {
					dockerImage = docker.build "simple-java-maven-app" + ":$BUILD_NUMBER"
				}	
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
	}
}

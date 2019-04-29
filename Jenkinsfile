pipeline {

	environment {
		registry = "https://cloud.docker.com/repository/docker/michaelkst/simple-java-maven-app"
		registryCredential = 'dockerhub'
		dockerImage = ''
	}
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    options {
        skipStagesAfterUnstable()
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
        stage('Building image and publish') {
		
			agent {
                docker { image 'node:7-alpine' }
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
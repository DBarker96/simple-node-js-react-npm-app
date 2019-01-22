pipeline {
    agent {
        docker {
            image 'node:6-alpine'
            args '-p 3000:3000'
        }
    }
    environment {
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
	stage('Sonar') {
	    steps {
	    	docker.image('sonarqube:latest').withRun('-p 9999:9000') { c ->
		     sh 'while ! ping http://localhost:9999 --silent; do sleep 1; done'
		     
		     def scannerHome = tool 'SQ Scanner'
		     withSonarQubeEnv('SQ Scanner') {
		     	env.SQ_HOSTNAME = 'http://localhost:9999';
			run SonarQube scan ...
		     }
		}
	    }
	}
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}

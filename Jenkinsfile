pipeline {
    agent any
       stages {
        stage('Build & Analysis') {
            steps {
                withSonarQubeEnv('colan-sonarqube-server') {
                sh 'mvn clean package sonar:sonar'
              }
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
 stage("Quality Gate"){
	 steps {
		script {
            timeout(time: 1, unit: 'MINUTES') {
       waitForQualityGate abortPipeline: true
       }
                  
	 }
 }
    }
    }
    post {
       always {
            echo "Pipeline finished with status: ${currentBuild.result}"
	        echo "Pipeline finished with status: ${currentBuild.result}"
	        echo "Pipeline finished with status: ${currentBuild.result}"
        }
    }
    }

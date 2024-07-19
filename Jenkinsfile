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
            timeout(time: 10, unit: 'MINUTES') {
             def QG = waitForQualityGate()
                    if (QG.status == 'ERROR') {
                       abortpipeline true
       }
                  
	 }
 }
    }
    }
       }
    post {
       always {
            echo "Pipeline finished with status: ${QG.status}"
        }
    }
    }

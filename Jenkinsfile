pipeline {
    agent any
	stage('Cleanup Workspace') {
    steps {
        deleteDir() 
    }
}
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
    }

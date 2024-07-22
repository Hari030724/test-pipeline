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
		    sh "mvn test"
		    sh "mvn test"
            }
        }
         stage('Cleanup Workspace') {
    steps {
        deleteDir() 
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
        failure {
            echo "Pipeline failed. Investigate and take necessary actions."
            currentBuild.result = 'FAILURE'
        }
    }
    }

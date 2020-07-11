#!groovy

pipeline {
    agent any

    /* Specifying a global properties */
    options {
    	// Specifying a global execution timeout, after which Jenkins will abort the Pipeline run
        timeout(time: 12, unit: 'MINUTES')
        // builds availables discarder from CI
        buildDiscarder(logRotator(artifactNumToKeepStr: '10', numToKeepStr: '20'))
    }

    /* Specifying listener's */
    triggers {
		// This will poll SCM for any changes every five minutes. 
		// If a change is detected since the last build it job will be triggered to build the changes.
		pollSCM 'H/30 * * * 1-5'
    }

    stages {

		stage('Checkout sources') {
            steps {
            	echo "-=- Pulling ${env.BRANCH_NAME} -=-"
                checkout scm
            }
        }

        stage('Package') {
            steps {
                echo "-=- compiling project -=-"
				sh './mvnw clean package '
            }
        }

        stage('Code inspection') {
        
            steps {
                echo "-=- run code inspection -=-"
                withSonarQubeEnv('SonarQube') {
					sh './mvnw sonar:sonar'
                }
            }
        }
    }

    post {
        always {
            echo "-=- One way or another, finished -=-"
            echo "-=- ${currentBuild.currentResult}: Job ${JOB_NAME}(${BUILD_NUMBER}) -=-"
        }
    }

}

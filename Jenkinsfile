pipeline {
    agent any
    stages {
        stage ('Checkout'){
            steps {
                git branch: "master", url: "https://github.com/edsherwin/docker-robot-framework.git", credentialsId: "edsherwin"
            }
        }
        stage ('Build') {
            steps {
                sh 'docker build -t rfdockerv1 .'
            }
        }
        stage ('Test') {
            agent { docker {
                image 'rfdockerv1:latest'
                args '--shm-size=1g -u root' }
            }
            environment {
                BROWSER = 'chrome'
                ROBOT_TESTS_DIR = "$WORKSPACE"
                ROBOT_REPORTS_DIR = "$WORKSPACE"
            }
            steps {
                sh '"/opt/robotframework/bin/run-tests-in-virtual-screen.sh"'
            }
        }
        //
    }
     post {
        	always {
		        script {
		          step(
			            [
			              $class              : 'RobotPublisher',
			              outputPath          : '.',
			              outputFileName      : '**/output.xml',
			              reportFileName      : '**/report.html',
			              logFileName         : '**/log.html',
			              disableArchiveOutput: false,
			              passThreshold       : 50,
			              unstableThreshold   : 40,
			              otherFiles          : "**/*.png,**/*.jpg",
			            ]
		          	)
		        }
	  		}		
	    }
}
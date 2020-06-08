pipeline {
    agent any
    stages {   
        stage ('Clean Workspace') {
            steps {
                cleanWs ()
            }
        }
        stage ('Checkout Repository'){
            steps {
                git branch: "master", url: "https://github.com/edsherwin/docker-robot-framework.git", credentialsId: "edsherwin"
            }
        }
        stage ('Build Image') {
            steps {
                sh 'docker build -t rfdockerv1 .'
            }
        }
        stage ('Test Execution') {
             agent { docker {
                image 'rfdockerv1:latest'
                // reuseNode = the same workspace created at top-level of the Pipeline
                reuseNode 'true'
                args '--shm-size=1g -u root' }
            }
            environment {
                BROWSER = 'chrome'
                ROBOT_TESTS_DIR = "$WORKSPACE"
                ROBOT_REPORTS_DIR = "$WORKSPACE/reports"
            }
            steps {
                sh '"/opt/robotframework/bin/run-tests-in-virtual-screen.sh"'
           // sh 'python3 -m rflint --ignore LineTooLong myapp'
		  //      	sh 'python3 -m robot.run --NoStatusRC --variable $WORKSPACE:test --outputdir reports test/'
		        	//sh 'python3 -m robot.run --NoStatusRC --variable $WORKSPACE:test --rerunfailed reports/output.xml --outputdir reports test/'
		  //      	sh 'python3 -m robot.rebot --merge --output reports/output.xml -l reports/log.html -r reports/report.html reports/output.xml reports/output.xml'
		        	sh 'exit 0'
            }
        }
        //Publish to Grafana
    //     stage ('Publish Report') {
    //             if (currentBuild.currentResult == 'UNSTABLE') {
    //                 currentBuild.result == "UNSTABLE"
    //             } 
    //             else {
    //                 currentBuild.result = "SUCCESS"
    //             }
    //             steps
    //             ([$class: 'InfluxDbPublisher', customData: null, customDataMap: null, customPrefix: null, target: 'grafana'])
    //     }
    //     catch (Exception e) {
    //     currentBuild.result = "FAILURE"
    //     step([$class: 'InfluxDbPublisher', customData: null, customDataMap: null, customPrefix: null, target: 'grafana'])
    //     }
     }
     //RobotFramework Test Results   
     post {
        	always {
		        script {
		          step (
			            [
			              $class              : 'RobotPublisher',
			              outputPath          : 'reports',
			              outputFileName      : 'output.xml',
			              reportFileName      : 'report.html',
			              logFileName         : 'log.html',
			              disableArchiveOutput: false,
			              passThreshold       : 50,
			              unstableThreshold   : 40,
			              otherFiles          : "**/*.png,**/*.jpg",
			            ]
		          	)
		        }
	  		}	
            //Grafana
            always {
                script {
                    if (currentBuild.currentResult == 'UNSTABLE') {
                    currentBuild.result == "UNSTABLE"
                } 
                else {
                    currentBuild.result = "SUCCESS"
                }
                step (
                    [
                        $class              : 'InfluxDbPublisher',
                        customData          : null,
                        customDataMap       : null,
                        customPrefix        : null,
                        target              : 'grafana'
                    ]
                )
            }	
	    }
    //post 
}

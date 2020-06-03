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
        stage ('Publish Results') {
            script {
                sh 'del "Results\\*.zip"'
                zip zipFile: 'results/results.zip', archive: false, dir: 'results', glob: '*.html'
                step (
                    [
                            $class              : 'RobotPublisher',
                            outputPath          : 'results',
                            outputFileName      : "output.xml",
                            reportFileName      : 'report.html',
                            logFileName         : 'log.html',
                            disableArchiveOutput: false,
                            passThreshold       : 95.0,
                            unstableThreshold   : 90.0,
                            otherFiles          : "**/*.png,**/*.jpg",  
                    ]
                )
                emailext body: '${SCRIPT, template="robot.template"}', subject: "[Jenkins] Robot Framework testresults for Docker Demo Project", to: 'nedsherwin@yahoo.com', recipientProviders: [[$class: 'CulpritsRecipientProvider']], attachmentsPattern: 'results/results.zip'
            }
        }
    }
}
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
            steps {
                script {
                // sh 'rm "results/*.zip"'
                // zip zipFile: 'results/results.zip', archive: false, dir: 'results', glob: '*.html'
                step(
                [
                $class              : 'RobotPublisher',
                outputPath          : 'robot_reports',
                outputFileName      : "**/output.xml",
                reportFileName      : '**/report.html',
                logFileName         : '**/log.html',
                disableArchiveOutput: false,
                passThreshold       : "${env.ROBOT_PASS_THRESHOLD}" as double,
                unstableThreshold   : "${env.ROBOT_UNSTABLE_THRESHOLD}" as double,
                otherFiles          : "**/*.png,**/*.jpg",
                ]
            )
              //  emailext body: '${SCRIPT, template="robot.template"}', subject: "[Jenkins] Robot Framework testresults for Docker Demo Project", to: 'nedsherwin@yahoo.com', recipientProviders: [[$class: 'CulpritsRecipientProvider']], attachmentsPattern: 'results/results.zip'
            }
        }
            }
    }
}
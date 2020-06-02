pipeline {
    agent none
    stages {
        stage('Functional regression tests') {
            agent { docker {
                image 'ppodgorsek/robot-framework:latest'
                args '--shm-size=1g -u root' }
            }
            environment {
                BROWSER = 'firefox'
                ROBOT_TESTS_DIR = "$WORKSPACE"
                ROBOT_REPORTS_DIR = "$WORKSPACE"
            }
            steps {
                sh '''
                    /opt/robotframework/bin/run-tests-in-virtual-screen.sh
                '''
            }
        }
    }
}
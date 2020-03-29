pipeline {
        agent any
        stages {
            stage('Functional regression tests') {
                steps {
                    sh "docker run --shm-size=1g -e BROWSER=firefox -v $WORKSPACE/robot-tests:/opt/robotframework/tests:Z -v $WORKSPACE/robot-reports:/opt/robotframework/reports:Z ppodgorsek/robot-framework:latest"
                }
            }
        }
    }
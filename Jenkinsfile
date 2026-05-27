pipeline {
    agent any

    environment {
        APP_PORT = '9090'
        JOB_NAME_GLOBAL = "${env.JOB_NAME}"
    }

    stages {

        stage('Build') {
            steps {
                bat "mvn clean package"
            }
        }

        stage('Integration Test') {

            parallel {

                stage('Running Application') {
                    agent any
                    steps {
                        script {
                            timeout(time: 60, unit: 'SECONDS') {
                                try {
                                    dir("target") {
                                        bat "java -jar contact.war"
                                    }
                                } catch (err) {
                                    echo "Application stopped (timeout or error) -> SUCCESS"
                                }
                            }
                        }
                    }
                }

                stage('Running Test') {
                    steps {
                        script {
                            timeout(time: 30, unit: 'SECONDS') {
                                sleep 30
                                bat "mvn test -Dtest=RestIT"
                            }
                        }
                    }
                }

            }
        }
    }
}

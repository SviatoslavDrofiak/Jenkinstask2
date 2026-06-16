pipeline {
    agent any

    environment {
        APP_PORT = '9090'
        JOB_NAME_GLOBAL = "${env.JOB_NAME}"
    }

    tools {
        jdk 'JDK17'
        maven 'Maven-3'
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Integration Test') {
            parallel {

                stage('Running Application') {
                    agent any

                    steps {
                        script {
                            try {
                                timeout(time: 60, unit: 'SECONDS') {

                                    dir('target') {
                                        sh 'java -jar contact.war --server.port=$APP_PORT'
                                    }

                                }
                            } catch (Exception e) {
                                echo 'Application stopped after timeout'
                            }
                        }
                    }
                }

                stage('Running Test') {
                    steps {
                        sleep 30

                        sh 'mvn test -Dtest=RestIT'
                    }
                }
            }
        }
    }

    post {
        success {
            echo "BUILD SUCCESS - ${JOB_NAME_GLOBAL}"
        }
        failure {
            echo "BUILD FAILED"
        }
    }
}

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
                sh 'mvn clean package -DskipTest'
            }
        }

        stage('Integration Test') {
            parallel {

                stage('Running Application') {
                    steps {
                        script {
                            try {
                                timeout(time: 60, unit: 'SECONDS') {
                                    dir('target') {
                                        sh 'ls -l'
                                        sh 'java -jar contact.war --server.port=9090'
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
                        sleep(time: 30, unit: 'SECONDS')
                        sh 'mvn test -Dtest=RestIT'
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished. Job: ${JOB_NAME_GLOBAL}"
        }
    }
}

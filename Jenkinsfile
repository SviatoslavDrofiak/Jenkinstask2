pipeline {
    agent any

    environment {
        APP_PORT = '9090'
        JOB_NAME_GLOBAL = "${env.JOB_NAME}"
    }

    tools {
        jdk 'JDK17'
        maven 'Maven3'
    }

    stages {

        stage('Build') {
            steps {
                bat 'mvn clean package -DskipTests'
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }

        stage('Integration Tests (Parallel)') {

            parallel {

                stage('Running Application') {
                    steps {
                        script {
                            timeout(time: 60, unit: 'SECONDS') {
                                try {
                                    dir("target") {
                                        echo "Starting application on port ${APP_PORT}"
                                        bat "java -jar contact.war --server.port=9091"
                                    }
                                } catch (err) {
                                    echo "Application stopped (timeout or error) -> CONTINUE PIPELINE"
                                }
                            }
                        }
                    }
                }

                stage('Running RestIT') {
                    steps {
                        script {
                            timeout(time: 30, unit: 'SECONDS') {

                                echo "Waiting for application to start..."
                                sleep 30

                                bat "mvn test -Dtest=RestIT"
                            }
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "BUILD SUCCESS - Job: ${JOB_NAME_GLOBAL}"
        }

        failure {
            echo "BUILD FAILED"
        }
    }
}

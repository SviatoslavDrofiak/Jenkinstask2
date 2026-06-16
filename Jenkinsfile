pipeline {
agent any

environment {
    APP_PORT = '9090'
    JOB_NAME_GLOBAL = "${env.JOB_NAME}"
}

stages {

    stage('Build') {
        steps {
            sh 'java -version'
            sh 'mvn -version'
            sh 'mvn clean package -DskipTests'
        }
    }

    stage('Unit Tests') {
        steps {
            sh 'mvn test'
        }
    }

    stage('Integration Test') {
        parallel {

            stage('Run Application') {
                steps {
                    script {
                        try {
                            timeout(time: 60, unit: 'SECONDS') {
                                dir('target') {
                                    sh 'ls -la'
                                    sh 'java -jar contact.war --server.port=${APP_PORT}'
                                }
                            }
                        } catch (Exception e) {
                            echo 'Application stopped after timeout'
                        }
                    }
                }
            }

            stage('Run RestIT') {
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
        echo "Job name: ${JOB_NAME_GLOBAL}"
    }
}

}

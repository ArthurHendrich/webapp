pipeline {
    agent any
    tools {
        maven 'Maven 3.3.9'
    }
    stages {
        stage('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    echo "MAVEN_HOME = ${MAVEN_HOME}"
                    mvn --version
                '''
            }
        }

        stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
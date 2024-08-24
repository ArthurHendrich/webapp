pipeline {
    agent any
    tools {
        maven 'Maven'
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
        
        stage('Check-Git-Secrets') {
            steps {
                sh 'rm -f trufflehog.json || true'
                sh 'docker run gesellix/trufflehog --json https://github.com/ArthurHendrich/webapp.git > trufflehog.json'
                sh 'cat trufflehog.json'
            }
        }

        stage('Source Composition Analysis') {
            steps {
                sh '''
                    rm owasp* || true
                    wget 'https://raw.githubusercontent.com/ArthurHendrich/webapp/master/owasp-dependency-check.sh'
                    chmod +x owasp-dependency-check.sh
                    bash owasp-dependency-check.sh
                    cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml
                '''
            }
        }

        stage('Static Application Security (SAST) - SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                    sh 'cat target/sonar/report-task.txt'
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy-To-Tomcat') {
            steps {
                sshagent(['tomcat']) {
                    sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@3.135.235.112:/home/ubuntu/prod/apache-tomcat-10.1.28/webapps/webapp.war'
                }
            }
        }
    }
}
pipeline { 
    agent any

    tools {
        jdk 'Java 17'  // 👈 This must match what you named it in Global Tool Configuration
    }

    environment {
        JAVA_HOME = "${tool 'Java17'}"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Build') { 
            steps {
                withMaven(maven : 'apache-maven-3.6.0') {
                    sh "mvn clean compile"
                }
            }
        }

        stage('Test') {
            steps {
                withMaven(maven : 'apache-maven-3.6.0') {
                    sh "mvn test"
                }
            }
        }

        stage('Build && SonarQube analysis') {
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh '/var/lib/jenkins/sonar-scanner-3.3.0.1492-linux/bin/sonar-scanner -Dsonar.projectKey=myProject -Dsonar.sources=./src'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy') {
            steps {
                withMaven(maven : 'apache-maven-3.6.0') {
                    sh "mvn deploy"
                }
            }
        }
    }
}

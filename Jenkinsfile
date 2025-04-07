pipeline { 
    agent any 
	
    tools {
        jdk 'jdk8'                     // This should match your JDK name in Jenkins
        maven 'apache-maven-3.6.0'     // This should match your Maven name
    }

    environment {
        JAVA_HOME = tool 'jdk8'
        PATH = "${env.JAVA_HOME}/bin:${env.PATH}"
    }
    stages {
        stage('Build') { 
            steps {
                withMaven(maven : 'apache-maven-3.6.0'){
                        sh "mvn clean compile"
                }
            }
        }
        stage('Test'){
            steps {
                withMaven(maven : 'apache-maven-3.6.0'){
                        sh "mvn test"
                }

            }
        }
        stage('build && SonarQube analysis') {
            steps {
                withSonarQubeEnv('sonar.tools.devops.****') {
                    sh 'sonar-scanner -Dsonar.projectKey=myProject -Dsonar.sources=./src'
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    // Requires SonarScanner for Jenkins 2.7+
                    waitForQualityGate abortPipeline: true
                }
            }
			}
        stage('Deploy') {
            steps {
               withMaven(maven : 'apache-maven-3.6.0'){
                        sh "mvn deploy"
                }

            }
        }
    }
}

pipeline {
    agent any
    tool {
         maven'mnv'
	 }
     environment {
        SONAR_URL = 'http://13.235.50.141:9000/'
        SONAR_PROJECT_KEY = 'java_project'
        SONAR_TOKEN = credentials('retail-token')
        NEXUS_URL = 'http://15.207.108.19:8081/'
        NEXUS_USERNAME = credentials('admin')
        NEXUS_PASSWORD = credentials('nexus')
    }
    stages {
        stage('Clone Repository') {
            steps {
                //git 'https://github.com/yatheesh-k/arzoo01.git'
                git branch: 'main', url:'https://github.com/yatheesh-k/java_sample.git'

                }
	     }
	     stage('Build') {
            steps {
                sh 'mvn clean install'
        
             }
         }

            stage('Test') {
            steps {
                sh 'mvn test'

            }
        }

        
     post {
        always {
            // cleanup steps, if any
            sh 'echo "Always do cleanup actions here"'
        }
        success {
            sh 'echo "Pipeline succeeded"'
        }
        failure {
            sh 'echo "pipeline failed"'
        }
    }
}

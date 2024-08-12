pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo/your-project.git'
                GITHUB_USERNAME='your-username'
                GITHUB_TOKEN='your-personal-access-token'

            }
        }
        stage('Build') {
            steps {
                script {
                    def mvnHome = tool name: 'Mvn', type: 'maven'
                    withMaven(maven: 'Mvn') {
                        sh "${mvnHome}/bin/mvn clean install"
                    }
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    def mvnHome = tool name: 'M3', type: 'maven'
                    withMaven(maven: 'M3') {
                        sh "${mvnHome}/bin/mvn test"
                    }
                }
            }
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


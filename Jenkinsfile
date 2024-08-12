pipeline {
    agent any

   stages {
        stage('Clone Repository') {
            steps {
                //git 'https://github.com/yatheesh-k/arzoo01.git'
                git branch: 'main', url: 'https://github.com/yatheesh-k/java_sample.git'
            }
        }
       stage('mvn install'){
            steps {
                 
                sh '''
                 ls -ltr
                 mvn install
                 '''
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


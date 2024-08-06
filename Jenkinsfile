pipeline {
    agent any
    tools {
         maven 'mvn'
	} 
    environment {
        NEXUS_URL = 'http://13.127.118.96:8081/repository/javaproject/'
        NEXUS_CREDENTIALS_ID = 'nexus' // ID of the stored credentials in Jenkins
    }
    stages {
        stage('Clone Repository') {
            steps {
                //git 'https://github.com/yatheesh-k/java_sample.git'
                git branch: 'main', url: 'https://github.com/yatheesh-k/java_sample.git'
            }
        }

        stage('mvn install') {
            steps {

                sh '''
                 ls -ltr
                mvn install
                 '''
             }
         }

        stage('mvn build') {
            steps {
                sh 'mvn build'
            }
        }
        stage('Zip Dist Directory') {
            steps {
                sh '''
                zip -r dist-${BUILD_ID}.zip dist
                '''
            }
        }
        stage('SonarQube analysis') {
            environment {
              SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
            withSonarQubeEnv('sonar') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=java project -Dsonar.sources=src -Dsonar.host.url=http://13.200.250.223:9000/ -Dsonar.login=sqp_dbd67b99e32634f4b5808a28ace6d937f2e3c6c6 "
                }
            }
        }
        stage('Upload Artifacts to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.NEXUS_CREDENTIALS_ID, passwordVariable: 'NEXUS_PASSWORD', usernameVariable: 'NEXUS_USERNAME')]) {
                    script {
                        def file = "dist-${env.BUILD_ID}.zip"
                        // Upload the file using HTTP Request Plugin
                        httpRequest(
                            httpMode: 'PUT',
                            acceptType: 'APPLICATION_JSON',
                            contentType: 'APPLICATION_OCTETSTREAM',
                            consoleLogResponseBody: true,
                            url: "${env.NEXUS_URL}${file}",
                            authentication: 'nexus',
                            requestBody: readFile(file)
                        )
                        sh 'rm -rf dist-${BUILD_ID}.zip'
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

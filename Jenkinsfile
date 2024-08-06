pipeline {
    agent any
    tool {
    maven 'mvn'
    }
    environment {
        NEXUS_URL = 'http://65.0.4.51:8081/'
        NEXUS_CREDENTIALS_ID = 'nexus' // ID of the stored credentials in Jenkins
    }
    stages {
        stage('Clone Repository') {
            steps {
                //git 'https://github.com/yatheesh-k/arzoo01.git'
                git branch: 'main', url: 'https://github.com/yatheesh-k/java_sample.git'
            }
        }

         stage('Build') {
            steps {
                sh 'mvn clean install'

             }
         }
   
        stage(' build') {
            steps {
                sh 'mvn run build'
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
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=java project -Dsonar.sources=src -Dsonar.host.url=http://52.66.209.200:9000 -Dsonar.login=sqp_3cf78ef11a74f81d85e989d7865ee0c76f468321"
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
}

pipeline {
    agent any
    environment {
        NEXUS_URL = 'http://13.126.115.190:8081/repository/java project/'
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
                sh 'mvn -B -DskipTests clean package' 
            }
        }
         stage('Test') { 
            steps {
               sh 'mvn test' 
            }
         }   
        stage('Package') {
            steps {
                script {
                     Maven package with specific goals
                    sh "${MAVEN_HOME}/bin/mvn package"
                }
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
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=java project -Dsonar.sources=src -Dsonar.host.url=http://13.232.87.27:9000/ -Dsonar.login=sqp_bddbc2147bd8de82a96ffed8ddc88a665eb3a699"
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

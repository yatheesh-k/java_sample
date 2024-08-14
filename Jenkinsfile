pipeline {
    agent any
    environment {
        NEXUS_URL = 'http://13.126.115.190:8081/repository/java-application-nexus/'
        NEXUS_CREDENTIALS_ID = 'nexus' // ID of the stored credentials in Jenkins
    }
    stages {
        stage('Clone Repository') {
            steps {
                //git 'https://github.com/yatheesh-k/arzoo01.git'
                git branch: 'main', url: 'https://github.com/yatheesh-k/java_sample.git'
            }
        }
   
       stage('Build WAR') { 
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
                    // Package the build output
                    // Example: create a zip archive
                    sh '''
                    mkdir -p output
                    cp -r target/* output/  # Adjust according to build output
                    zip -r output-${BUILD_ID}.zip output
                    '''
                }
            }
        }
        stage('SonarQube analysis') {
            environment {
              SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
            withSonarQubeEnv('sonar') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=java_project -Dsonar.sources=src -Dsonar.host.url=http://13.232.87.27:9000/ -Dsonar.login=sqp_0d85a730478faecb9fe4d2aa1c54f4922d01b23a"
                }
            }
        }
    stage('Upload WAR to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.NEXUS_CREDENTIALS_ID, passwordVariable: 'NEXUS_PASSWORD', usernameVariable: 'NEXUS_USERNAME')]) {
                    script {
                        def warFile = "build/libs/my-app-${env.BUILD_ID}.war" // Update path and filename
                        def response = httpRequest(
                            httpMode: 'PUT',
                            acceptType: 'APPLICATION_JSON',
                            contentType: 'APPLICATION_OCTETSTREAM',
                            consoleLogResponseBody: true,
                            url: "${env.NEXUS_URL}/${warFile}",
                            authentication: env.NEXUS_CREDENTIALS_ID,
                            requestBody: readFile(warFile)
                        )
                        
                        echo "Response: ${response}"
                    }
                }
            }
        }
    }


    post {
        success {
            sh 'echo "Pipeline succeeded"'
        }
        failure {
            sh 'echo "pipeline failed"'
        }
    }
}

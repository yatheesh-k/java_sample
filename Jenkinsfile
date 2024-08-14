pipeline {
    agent any
    environment {
        NEXUS_URL = 'http://13.235.245.152:8081/repository/java-application-nexus/'
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
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=java_project -Dsonar.sources=src -Dsonar.host.url=http://13.234.110.246:9000/ -Dsonar.login=sqp_0d85a730478faecb9fe4d2aa1c54f4922d01b23a"
                }
            }
        }
   stage('Upload WAR to Nexus') {
            steps {
                script {
                    def warFilePath = 'build/libs/*.war'
                    def nexusUploadUrl = "${env.NEXUS_URL}/repository/${env.NEXUS_REPO}/'build/libs/*.war'
                    
                    // Upload the WAR file
                    httpRequest(
                        url: nexusUploadUrl,
                        httpMode: 'PUT',
                        authentication: env.NEXUS_CREDENTIALS_ID,
                        contentType: 'APPLICATION_OCTETSTREAM',
                        requestBody:readFile(warFilePath)
                        )
                        echo "Response: ${response}"
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

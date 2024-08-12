pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://52.66.102.44:8081/repository/java project/'
        NEXUS_CREDENTIALS_ID = 'nexus' // ID of the stored credentials in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
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

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
       
         stage('Zip Dist Directory') {
            steps {
                dir("/var/lib/jenkins/workspace/demopipelinetask/my-app") {
                    sh '''
                        zip -r dist-${BUILD_ID}.zip dist
                        mvn -B -DskipTests clean package
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
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=java project -Dsonar.sources=src -Dsonar.host.url=http://65.1.64.232:9000/ -Dsonar.login=sqp_bddbc2147bd8de82a96ffed8ddc88a665eb3a699"
                }
            }
        }

        stage('Upload Artifacts to Nexus') {
    steps {
        withCredentials([usernamePassword(credentialsId: env.NEXUS_CREDENTIALS_ID, passwordVariable: 'NEXUS_PASSWORD', usernameVariable: 'NEXUS_USERNAME')]) {
            script {
                def file = "dist-${env.BUILD_ID}.zip"
                // Check if the file exists before attempting to upload
                if (fileExists(file)) {
                    // Upload the file using HTTP Request Plugin
                    def response = httpRequest(
                        httpMode: 'PUT',
                        acceptType: 'APPLICATION_JSON',
                        contentType: 'APPLICATION_OCTETSTREAM',
                        consoleLogResponseBody: true,
                        url: "${env.NEXUS_URL}${file}",
                        authentication: env.NEXUS_CREDENTIALS_ID,
                        requestBody: readFile(file)
                    )
                        
                        sh 'rm -rf dist-${env.BUILD_ID}.zip'
                    }
                }
            }
        }
    }

      post {
        always {
            // Cleanup steps, if any
            sh 'echo "Always do cleanup actions here"'
        }
        success {
            sh 'echo "Pipeline succeeded"'
        }
        failure {
            sh 'echo "Pipeline failed"'
        }
    }


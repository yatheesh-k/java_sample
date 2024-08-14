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
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=java_project -Dsonar.sources=src -Dsonar.host.url=http://13.234.59.209:9000/ -Dsonar.login=sqp_0d85a730478faecb9fe4d2aa1c54f4922d01b23a"
                }
            }
        }
   stage ("Upload Artifact") {
            steps {
                nexusArtifactUploader(
                  nexusVersion: '0.0.0.0',
                  protocol: 'http',
                  nexusUrl: "${NEXUS_IP}:${NEXUS_PORT}",
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: "${java-application-nexus}",
                  credentialsId: "${NEXUS_LOGIN}",
                  artifacts: [
                    [artifactId: 'java-application-nexus',
                     classifier: '',
                     file: 'target/practise1.war',
                     type: 'war']
                  ]
                )
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

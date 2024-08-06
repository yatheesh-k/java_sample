pipeline {
    agent any
    tools {
         maven 'mvn'
	} 
    
    environment {
        SONAR_URL = 'http://13.235.50.141:9000/'
        SONAR_PROJECT_KEY = 'java project'
        SONAR_TOKEN = credentials('sqp_dbd67b99e32634f4b5808a28ace6d937f2e3c6c6 ')
        NEXUS_URL = 'http://15.207.108.19:8081/'
        NEXUS_USERNAME = credentials('admin')
        NEXUS_PASSWORD = credentials('nexus')
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

        stage('build') {
            steps {
	      // Build the project using Maven 
                sh 'mvn clean install'
            }
        }
	stage('Test') {
            steps {
                // Run tests
                sh 'mvn test'
            }
        }
        
         stage('SonarQube Analysis') {
            steps {
                sh "mvn sonar:sonar -Dsonar.host.url=http://13.235.50.141:9000/ -Dsonar.login=sqp_dbd67b99e32634f4b5808a28ace6d937f2e3c6c6 -Dsonar.projectKey=java project"
               }
           }
                
        
         stage('Package') {
            steps {
                sh 'mvn package'
               }
            }

        stage('Upload to Nexus') {
            steps {
                sh "curl -u $NEXUS_USERNAME:$NEXUS_PASSWORD --upload-file target/backend.jar $NEXUS_URL/repository/releases/com/yourcompany/backend/backend.jar"
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

   pipeline {
    agent any
    tools {
        maven 'mvn'
    }

    environment {
        SONAR_URL = 'http://13.235.50.141:9000/'
        SONAR_PROJECT_KEY = 'java_project'
        SONAR_TOKEN = credentials('sonar-token')
        NEXUS_URL = 'http://15.207.108.19:8081/'
        NEXUS_USERNAME = credentials('nexus-username')
        NEXUS_PASSWORD = credentials('nexus-password')
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo/java_sample.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                sh "mvn sonar:sonar -Dsonar.host.url=${SONAR_URL} -Dsonar.login=${SONAR_TOKEN} -Dsonar.projectKey=${SONAR_PROJECT_KEY}"
            }
        }

        stage('Publish to Nexus') {
            steps {
                sh '''
                mvn deploy:deploy-file \
                    -Durl=${NEXUS_URL}/repository/maven-releases/ \
                    -DrepositoryId=nexus-releases \
                    -Dfile=target/your-artifact.jar \
                    -DgroupId=com.example \
                    -DartifactId=your-artifact \
                    -Dversion=1.0.0
                '''
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

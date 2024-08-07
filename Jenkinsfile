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
      }
    }  

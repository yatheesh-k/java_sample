pipeline {
    agent any

   stages {
        stage('Clone Repository') {
            steps {
                //git 'https://github.com/yatheesh-k/arzoo01.git'
                git branch: 'main', url: 'https://github.com/yatheesh-k/java_sample.git'
            }
        }
       stage('mvn install'){
            steps {
                 
                sh '''
                 ls -ltr
                 mvn install
                 '''
             }
         }

        //stage('Build') {
          //  steps {
                    // Use a valid Maven goal
            //        sh 'mvn clean package'
       stage('Build') {
            steps {
                dir("/var/lib/jenkins/workspace/demopipelinetask/my-app") {
                sh 'mvn -B -DskipTests clean package'
                }
            }
       }
          stage('Zip Dist Directory') {
            steps {
                sh '''
                sh 'zip -r dist-${BUILD_ID}.zip dist'
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

pipeline {
   agent any
    tools {
        nodejs 'nodeRecent' 
    }
    environment { 
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'chmod +x ./jenkins/scripts/test.sh'            
                sh './jenkins/scripts/test.sh'
            }      
        }

        stage('Deploy for production') {       
            steps {
                sh 'chmod -R +x ./jenkins/scripts'
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }      
    }
}

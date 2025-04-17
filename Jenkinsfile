pipeline {
    agent any
    
    tools {
        maven "maven"
    }
    
    environment{
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                checkout scmGit(branches: [[name: '**']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/srinunali/CI-CD-Deployment.git']])
            }
        }
        
        stage('Maven compile'){
            steps{
                sh "mvn clean compile"
            }
        } 
        
        stage('Maven test'){
            steps{
                sh "mvn clean test"
            }
        }  
        
        stage('SonarQube Analysis'){
            steps{
               withSonarQubeEnv('sonar-server') {
            sh ''' $CANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-app  -Dsonar.projectKey=Blogging-app -Dsonar.java.binaries=target  '''
                }
            }
        } 
        
    }
}

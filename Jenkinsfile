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
        
        stage('Trivy scan'){
            steps{
                
                sh "trivy fs --format table -o fs.html ."
                /* script{
                def trivyoutput = sh(script: "trivy fs  .", returnStdout: true).trim()
                    println trivyOutput
                } */
            }
            
        }
        
        stage('SonarQube Analysis'){
            steps{
               withSonarQubeEnv('sonar-server') {
            sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=CI_CD-Deployment  -Dsonar.projectKey=CI_CD-Deployment -Dsonar.java.binaries=target  '''
}
            }
        } 
        
    }
}

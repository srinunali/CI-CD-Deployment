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
                sh "mvn compile"
            }
        } 
        
        stage('Maven test'){
            steps{
                sh "mvn test"
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
            
            stage('Maven Build'){
                steps{
                    sh "mvn package"
                }
            }
            
            stage ('Docker Image build'){
                steps{
                    script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                         sh "docker build -t srinu1995/srinivas:latest ."
                      }
                    }
                }
            }
            
            stage('Image push to repo'){
                steps{
                    script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker')  {
                         sh "docker push srinu1995/srinivas:latest"
                      }
                    }
                }
            }
            
            stage('Trivy image scan'){
            steps{
                sh "trivy image --format table -o fs.html srinu1995/srinivas:latest"
                
                 }
            }
            
            stage('K8s Deployment'){
                steps{
                    withKubeConfig(caCertificate: '', clusterName: 'minikube', contextName: 'minikube', credentialsId: 'k8s-secret', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://192.168.77.2:8443') {
                          sh "kubectl apply -f ./k8s-files/deployment-service.yml -n webapps"
                    } 
                }
            }
            
            stage('K8s Deployment check'){
                steps{
                   withKubeConfig(caCertificate: '', clusterName: 'minikube', contextName: 'minikube', credentialsId: 'k8s-secret', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://192.168.77.2:8443')  {
                          sh "kubectl get pods -n webappps"
                          sh "kubectl get svc -n webappps"
                    } 
                }
            }
            
            
    }
}

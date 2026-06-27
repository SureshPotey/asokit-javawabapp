pipeline {
    agent any

    stages {
        stage('git') {
            steps {
                git 'https://github.com/SureshPotey/asokit-javawabapp.git'
            }
        }
        stage('Build'){
            steps{
                sh 'mvn clean package'
            }
       }
       stage('sonarqube QC'){
           steps{
               withSonarQubeEnv ('SonarQube'){
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar'
               }        
           }
       }
       stage('Aritifact Nexus'){
           steps{
              nexusArtifactUploader artifacts: [[artifactId: 'suri-app', classifier: '', file: 'target/maven-web-app.war', type: 'war']], credentialsId: 'Nexus', groupId: 'in.ashokit', nexusUrl: '13.233.157.76:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'ashok', version: '3.0-RELEASE' 
           }
       }
       stage('container Image'){
           steps{
               sh 'docker build -t poteysuresh/asokit .'
           }
       }
       stage('docker push'){
           steps{
               withCredentials([string(credentialsId: 'dockerpasswd', variable: 'dockerpasswd')]) {
                   sh "docker login -u poteysuresh -p ${dockerpasswd}"
                   sh 'docker push poteysuresh/asokit'
               }
           }
       }
       stage('deployment on cluster'){
           steps{
               sh 'kubectl apply -f k8s-deploy.yml'
           }
       }
    }
}

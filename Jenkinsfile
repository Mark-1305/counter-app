pipeline{

    agent any
    stages{
        stage('Git Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/Mark-1305/counter-app.git'
            }
        }
        stage('Unit Testing'){
            steps{
                sh 'mvn test'
            }
        } 
        stage('Integration Test'){
            steps{
                sh 'mvn verify -DskipUnitTests '
            }
        }
        stage('Maven Build'){
            steps{
                sh 'mvn clean install'
            }
        } 
        stage('Static Code Analysis'){
            steps{
                script{
                withSonarQubeEnv(credentialsId: 'sonar-updated-api') {
                    sh "mvn clean package sonar:sonar"
                    }                    
                }
            }
        } 
        stage('Quality Gate Status'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-auth-token'
                } 
            }
        } 
        stage('Upload jar file to Nexus'){
            steps{
                script{
                    nexusArtifactUploader artifacts: 
                    [
                        [artifactId: 'springboot', 
                        classifier: '', 
                        file: 'target/Uber.jar', 
                        type: 'jar']
                        ], 

                    credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '10.0.8.74:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'demo-app-release', 
                    version: "1.0.0"

                }
            }
        } 
    }
}

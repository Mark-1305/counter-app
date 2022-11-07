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
                    def readpomVersion = readMavenPom file: 'pom.xml'   
                    def nexusRepo = readpomVersion  .version.endsWith('SNAPSHOT') ? "app-realease-snapshot" : "app-release"
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
                    repository: nexusRepo, 
                    version: "${readpomVersion.version}"

                }
            }
        } 
        stage('Docker Image Build'){
            steps{                
                script{
                    sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID manoharshetty507/$JOB_NAME:v1.$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID manoharshetty507/$JOB_NAME:latest'
                
                }                
            }
        }
        stage('Docker Image Push to DockerHUB'){
            steps{                
                script{
                    withCredentials([string(credentialsId: 'docker_creds', variable: 'docker_hub_cred')]){
                        sh 'docker login -u manoharshetty507 -p ${docker_hub_cred}'
                        sh 'docker image push manoharshetty507/$JOB_NAME:v1.$BUILD_ID'
                        sh 'docker image push manoharshetty507/$JOB_NAME:latest'                    
                    }                 
                }                
            }
        }   
    }
}

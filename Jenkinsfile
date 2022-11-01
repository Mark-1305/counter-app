pipeline{

    agent any
      tools { 
      maven 'M2_HOME' 
      jdk 'JAVA_HOME'
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
    }
}

pipeline {

    agent any
    tools{
        maven 'maven'
    }

    stages{
       stage('GitCheckout'){
          steps{
             git branch: 'main', changelog: false, poll: false, url: 'https://github.com/balu1123/counter-demo.git'
          } 
       }  

       stage('UNIT TESTING'){
           steps{
              sh 'mvn test'
           }  
       }

       stage('Integration Testing'){
           steps{
              sh 'mvn verify -DskipUnitTests'
           }
       }   

       stage('Maven Build'){
           steps{
              sh 'mvn clean install'
           }
       }  

       stage('Sonar Quality Test'){
          steps{
                withSonarQubeEnv(credentialsId: 'sonar_cred') {
                   sh 'mvn clean package sonar:sonar'
                }
             }
          }

    }
}        
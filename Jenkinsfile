pipeline {

    agent any
    tools{
        maven 'maven'
    }

    stages{
       stage("GitCheckout"){
          steps{
             git branch: 'main', changelog: false, poll: false, url: 'https://github.com/balu1123/counter-demo.git'
          } 
       }  

       stage("UNIT TESTING"){
           steps{
              sh 'mvn test'
           }  
       }

       stage("Integration Testing"){
           steps{
              sh 'mvn verify -DskipUnitTests'
           }
       }   

       stage("Maven Build"){
           steps{
              sh 'mvn clean install'
           }
       }  
        
       stage("Sonarqube analysis"){
          steps{
             script{
                withSonarQubeEnv(credentialsId: 'sonar_cred') {
                sh 'mvn clean package sonar:sonar'
               }
             }
          }
       } 

       stage("Quality Gate"){
         steps{
            script{
               waitForQualityGate abortPipeline: false, credentialsId: 'sonar_cred'
            }
         }
       }

       stage("OWASP Dependency check"){
          steps{
             script{
               dependencyCheck additionalArguments: '', odcInstallation: 'DP'
               dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
             }
          }
       }

      stage("Nexus Artifact Repo"){
	     steps{
		   script{
            def pom = readMavenPom file: 'pom.xml'
			   def nexusRepo = pom.version.endsWith("SNAPSHOT") ? "demoapp-SNAPSHOT" : "demoapp-release"
			   nexusArtifactUploader artifacts:
			   [
				[artifactId: 'springboot',
				 classifier: '',
				 file: 'target/Uber.jar',
				 type: 'jar']
			    ],
				credentialsId: 'nexus_cred',
				groupId: 'com.example',
				nexusUrl: '3.90.254.135:8081',
				nexusVersion: 'nexus3',
				protocol: 'http',
				repository: nexusRepo,
				version: "${pom.version}"
		 }
	   }	
	 }
   }
}        
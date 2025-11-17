pipeline {

   agent any

   tools{
      maven 'maven-3.9'
   }

   stages {
      stage('Clone') {
         steps {
            script {
               echo "📦 Récupération du code source..."
               checkout scm
            }	 
         }
      }
	  stage('Compile'){
         steps{
         	sh "mvn compile"
         }
      }
      stage('Test'){
         steps{
        	sh "mvn test"
         }
      }
      
      stage('Build'){
         steps{
            sh "mvn -B -DskipTests clean install"
         }
      }
   }
}

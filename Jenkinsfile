pipeline {
    
    agent {
        node {
            label 'java' 
        }
    }

    tools {
        maven "M3"
    }

    stages {
        stage('Checking out the repo') {
            steps {
                checkout changelog: true, poll: true, scm: [$class: 'GitSCM', branches: [[name: '*/*']], 
                extensions: scm.extensions, userRemoteConfigs: [[credentialsId: 'github_token', url: 'https://github.com/jpenekusu/sample-java-project.git']]]
                sh "ls -lart ./"    
            }
        }
        stage('Compling ...') {
            steps {
                withMaven(maven:'M3') {
                    sh "mvn compile"
                }
            }
        }        
        stage('Testing ...') {
            when {
                // skip this stage unless on Master branch
                not {
                    branch "master"
                }                    
            }                    
            steps {
                sh "mvn test"
            }
        }        
        stage('Building ...') {
            steps {
                sh "mvn -B -DskipTests clean package"
            }
        }
        stage('Code coverage') {
            when {
                // skip this stage unless on Master branch
                branch "master"
            }                    
            steps {        
                withMaven(maven:'M3') {
                    sh "mvn clean cobertura:cobertura install test -Dcobertura.report.format=xml"                    
                }
            }
            post {        
                always {
                    junit '**/test-reports/*.xml'
                    step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/coverage.xml', failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 2, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])

                }
            }
        }
        stage('Building and sending results to Sonar ...') {
            when {
                // skip this stage unless on Master branch
                branch "master"
            }            
            steps {
                withSonarQubeEnv(installationName: 'SonarInstall', credentialsId: 'sonarqube_token') {
                    sh 'mvn -B -DskipTests clean package sonar:sonar'
                }
            }
        }       
    }
}

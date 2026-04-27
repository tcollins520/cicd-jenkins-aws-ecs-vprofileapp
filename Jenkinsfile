pipeline {
    agent any
    tools {
        maven "MAVEN3.9"
        jdk "JDK17"
    }


    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        imageName = "788143860357.dkr.ecr.us-east-1.amazonaws.com/vprofileappimg"
        vprofileRegistry = "https://788143860357.dkr.ecr.us-east-1.amazonaws.com"
        cluster = "vprofile-tc"
        service = "vprofileappsvc"
    }
  # list of stages to be executed in the pipeline. Give each stage a name and the steps to be executed in that stage.
  stages {
   
        stage('Fetch code') {
            steps {
               git branch: 'main', url: 'https://github.com/tcollins520/cicd-jenkins-aws-ecs-vprofileapp.git'
            }

        }


        stage('Build'){
            steps{
               sh 'mvn install -DskipTests'
            }

            post {
               success {
                  echo 'Now Archiving it...'
                  archiveArtifacts artifacts: '**/target/*.war'
               }
            }
        }

        stage('UNIT TEST') {
            steps{
                sh 'mvn test'
            }
        }

        stage('Checkstyle Analysis') {
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }
        # code analysis with sonarQube, make sure to have the sonarQube server running and configured in Jenkins global tools configuration
        # also make sure to have the sonarQube plugin installed in Jenkins and configured with the correct server details and credentials
        # code analysis improves code quality and helps to identify potential bugs and vulnerabilities in the codebase, it also provides insights on code coverage and code smells which can help to improve the overall quality of the application
        # after the code analysis stage, we can also add a quality gate stage to ensure that the code meets the defined quality standards before proceeding with the build and deployment stages
        # the results of the code analysis can be viewed in the sonarQube dashboard, which provides detailed information on the code quality and helps to track improvements over time
        stage("Sonar Code Analysis") {
            environment {
                scannerHome = tool 'sonar6.2'
            }
            steps {
              withSonarQubeEnv('sonarserver') {
                sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
              }
            }
        }

        stage('Build App Image') {
          steps {
       
            script {
                dockerImage = docker.build( imageName + ":$BUILD_NUMBER", "./Docker-files/app/multistage/")
                }
          }
    
        }

        stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( vprofileRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
        }
        stage('Remove Container Images'){
            steps{
                sh 'docker rmi -f $(docker images -a -q)'
            }
        }


        stage('Deploy to ecs') {
          steps {
            withAWS(credentials: 'awscreds', region: 'us-east-1') {
            sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
               }
          }
        }


  }
}

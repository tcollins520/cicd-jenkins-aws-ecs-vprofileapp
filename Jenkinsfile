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

    stages {

        stage('Fetch Code') {
            steps {
                git branch: 'main', url: 'https://github.com/tcollins520/cicd-jenkins-aws-ecs-vprofileapp.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean install'
            }
            post {
                success {
                    echo 'Archiving artifact...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Checkstyle Analysis') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage("Sonar Code Analysis") {
            environment {
                scannerHome = tool 'sonar6.2'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh """
                    ${scannerHome}/bin/sonar-scanner \
                      -Dsonar.projectKey=vprofile \
                      -Dsonar.projectName=vprofile \
                      -Dsonar.projectVersion=1.0 \
                      -Dsonar.sources=src/ \
                      -Dsonar.java.binaries=target/classes \
                      -Dsonar.junit.reportsPath=target/surefire-reports/ \
                      -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                      -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
                    """
                }
            }
        }

        stage('Build App Image') {
            steps {
                script {
                    dockerImage = docker.build(imageName + ":${BUILD_NUMBER}", "./Docker-files/app/multistage/")
                }
            }
        }

        stage('Upload App Image') {
            steps {
                script {
                    docker.withRegistry(vprofileRegistry, registryCredential) {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Remove Container Images') {
            steps {
                sh 'docker rmi -f $(docker images "${imageName}" -q) || true'
            }
        }

        stage('Deploy to ECS') {
            steps {
                withAWS(credentials: 'awscreds', region: 'us-east-1') {
                    sh """
                    aws ecs update-service \
                      --cluster ${cluster} \
                      --service ${service} \
                      --force-new-deployment
                    """
                }
            }
        }
    }
}
pipeline {
    agent any
    environment {
        TAG = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('Code Checkout') {
            agent { 
                label 'sonnar-qube'
            }
            steps {
                git 'https://github.com/Nikkii30/Employee-App-DevOps-Training.git'
            }
        }
        stage('SonarQube Analysis') {
            agent { 
                label 'sonnar-qube'
            }
            steps {
                withSonarQubeEnv('sonnar-qube') {
                echo env.BUILD_NUMBER
                echo "${TAG}"
                sh "/usr/bin/envsubst < sonar-project.properties > /tmp/sonar-project.properties"
                sh "mv /tmp/sonar-project.properties sonar-project.properties"
                sh "cat sonar-project.properties"
                sh "/opt/sonar-scanner/bin/sonar-scanner"
            }
            }
        }

        stage('Code Build') {
            steps {
                sh "/usr/local/bin/mvn clean package"
            }
        }

        stage('Publish to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'employees-app', classifier: '', file: 'target/employees-app-1.0.${BUILD_NUMBER}.war', type: 'war']], credentialsId: 'nexus', groupId: 'com.example.employees', nexusUrl: 'localhost:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'war_files', version: '1.0.${BUILD_NUMBER}'
            }
        }
    }
}

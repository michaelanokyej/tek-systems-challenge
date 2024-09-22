pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS_ID = 'azure-creds'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                script {
                    // Example: Build a Java application
                    sh './mvnw clean package'
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    // Using OWASP ZAP for a security scan
                    sh 'zap-baseline.py -t http://localhost:8080 -r zap-report.html'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh './mvnw test'
                }
            }
        }
        stage('Deploy to Azure') {
            steps {
                script {
                    // Assuming the deployment script is already in place
                    sh './deploy_to_azure.sh'
                }
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            archiveArtifacts artifacts: 'zap-report.html', fingerprint: true
        }
        success {
            mail to: 'team@example.com',
                subject: "Build Success: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                body: "Good news, ${env.JOB_NAME} build ${env.BUILD_NUMBER} was successful."
        }
        failure {
            mail to: 'team@example.com',
                subject: "Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                body: "Alert: ${env.JOB_NAME} build ${env.BUILD_NUMBER} failed."
        }
    }
}

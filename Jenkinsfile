
pipeline {
    agent any

    tools {
        maven "maven-3.9.14"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'dev', url: 'https://github.com/Rammajjari/maven-webapplication-project-kkfunda.git'
            }
        }

        stage('Build') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('SQ Scanning') {
            steps {
                sh "mvn sonar:sonar"
            }
        }

        stage('Nexus Artifacts') {
            steps {
                sh "mvn deploy"
            }
        }

        stage('Tomcat Deployment') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'tomcat-cread-1',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                    curl -u $USER:$PASS \
                    --upload-file target/maven-web-application.war \
                    "http://13.232.71.91:8080/manager/text/deploy?path=/maven-web-application&update=true"
                    '''
                }
            }
        }
    }

    post {

        always {
            echo "Pipeline completed"
        }

        success {
            slackSend(
                channel: '#amazon-project',
                color: 'good',
                message: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} completed successfully.\nURL: ${env.BUILD_URL}"
            )
        }

        failure {
            slackSend(
                channel: '#amazon-project',
                color: 'danger',
                message: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER} failed.\nCheck: ${env.BUILD_URL}"
            )
        }

        unstable {
            slackSend(
                channel: '#amazon-project',
                color: 'warning',
                message: "⚠️ UNSTABLE: ${env.JOB_NAME} #${env.BUILD_NUMBER} is unstable.\nURL: ${env.BUILD_URL}"
            )
        }
    }
}

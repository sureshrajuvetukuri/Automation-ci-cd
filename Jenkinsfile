pipeline {
    agent any
    tools{
        maven 'maven'
    }
    environment {
        value = "${env.DEV}:${env.STAGING}:${env.BETA}:${env.LIVE}"
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/sureshrajuvetukuri/Automation-ci-cd.git']]])
                sh 'mvn clean install, ${value}'
            }
        }
    
        stage("unit test") {
            steps {
                sh 'echo Hello, ${value}'
            }
        }
        stage('sonar quality check'){
            script {

                    withSonarQubeEnv(credentialsId: 'sonar-token'){

                        sh 'mvn clean package sonar:sonar, ${value}'
                    }
                }
            }

        stage('Quality Gate status'){

            steps{

                script{

                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token' 
                }
            }
        }
    post {
        always {
            sendSlackNotifcation()
            slackSend botUser: true, 
            channel: 'builds', 
            color: '#00ff00', 
            message: 'Testing Jekins with Slack', 
            tokenCredentialId: 'slack-token'
        }    
    }
}

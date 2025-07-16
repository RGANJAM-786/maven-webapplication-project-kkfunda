pipeline {
    agent any

    tools {
        maven "maven-3.9.9"
    }

    stages {

        stage('NotifyStart') {
            steps {
                script {
                    slackSend(color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})", channel: '#devops-practice')
                }
            }
        }

        stage('GitCheckout') {
            steps {
                git branch: 'prod', url: 'https://github.com/RGANJAM-786/maven-webapplication-project-kkfunda.git'
            }
        }

        stage('Build') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('CodeQuality') {
            steps {
                sh "mvn sonar:sonar"
            }
        }

        stage('NexusUpload') {
            steps {
                sh "mvn clean deploy"
            }
        }

        stage('DeployToTomcat') {
            steps {
                sh """
                    curl -u rakesh:password \\
                    --upload-file target/maven-web-application.war \\
                    "http://34.201.247.138:8080/manager/text/deploy?path=/maven-web-application&update=true"
                """
            }
        }
    }

    post {
        always {
            script {
                def buildStatus = currentBuild.currentResult ?: 'SUCCESS'
                def colorCode
                def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
                def summary = "${subject} (${env.BUILD_URL})"

                switch (buildStatus) {
                    case 'SUCCESS':
                        colorCode = '#00FF00'
                        break
                    default:
                        colorCode = '#FF0000'
                }

                slackSend(color: colorCode, message: summary, channel: '#devops-practice')
            }
        }
    }
}

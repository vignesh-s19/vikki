pipeline {
    agent any    
    
    environment {
        // Set your GitHub credentials ID here
        GITHUB_CREDENTIALS = credentials('Github-Token-Jenkins-Mail-CICD')
    }
    

    stages {
        stage('Notification') {
            steps {
                script {
                    emailext (
                        subject: "Pipeline Started: ${currentBuild.fullDisplayName}",
                        body: "The ${currentBuild.fullDisplayName} pipeline has started on ${env.NODE_NAME}.",
                        to: 'tamilbecse139@gmail.com,vignesh-s19.sivasubramanian@quadgen.com'
                    )
                }
            }
        }

        // stage('Checkout') {
        //     steps {
        //         checkout scm
        //     }
        // }
        stage('Capture Pull Request Details') {
            steps {
                script {
                    def gitCommit = sh(script: 'git log --format=%H -n 1', returnStdout: true).trim()
                    def prDetailsJson = sh(script: "curl -sSL https://api.github.com/repos/vignesh-s19/vikki/commits/$gitCommit/pulls", returnStdout: true).trim()

                    // Parse the JSON response using JsonSlurper
                    def jsonSlurper = new groovy.json.JsonSlurper()
                    def prDetails = jsonSlurper.parseText(prDetailsJson)

                    // Extract title and body
                    def prTitle = prDetails.title
                    def prBody = prDetails.body
                    def prID = prDetails.id

                    // Set environment variables
                    env.PULL_REQUEST_TITLE = prTitle
                    env.PULL_REQUEST_BODY = prBody
                    env.PULL_REQUEST_ID = prID
                }
            }
        }
        stage('Echo Pull Request Details') {
            steps {
                echo "Pull Request Title: ${env.PULL_REQUEST_TITLE}"
                echo "Pull Request Body: ${env.PULL_REQUEST_BODY}"
                echo "Pull Request ID: ${env.PULL_REQUEST_ID}"
            }
        }

        
        stage('Send Email') {
            steps {
                script {                  
                    emailext body: "Pull Request Details:\n" +
                                   "PR Number: ${PULL_REQUEST_BODY}\n" +
                                   "PR Creator: ${PULL_REQUEST_TITLE}\n" +
                                   "Commit Message: ${commitMessage}",
                             subject: "Pull Request Details for PR#${prNumber}",
                             to: 'tamilbecse139@gmail.com,vigneshwaran.sivasubramanian@quadgen.com',
                             mimeType: 'text/plain',

                        }
              }
        }
    }
}

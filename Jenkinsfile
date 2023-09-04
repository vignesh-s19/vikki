pipeline {
    agent any    
    
    environment {
        // Set your GitHub credentials ID here
        GITHUB_CREDENTIALS = credentials('Vikki-Github')
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
        //         // Checkout the code from your SCM (e.g., Git)
        //         checkout scm
        //     }
        // }
        
        stage('Print PR Details') {
            steps {
                script {
                    def prNumber = env.CHANGE_ID // Jenkins environment variable for PR number
                    
                    // Fetch PR details
                    def prDetails = sh(script: """
                        curl -u github-credentials \
                        -s https://api.github.com/repos/vignesh-s19/vikki/pulls/${prNumber}
                    """, returnStatus: false, returnStdout: true).trim()
                    
                    // Fetch the commit message
                    def commitMessage = sh(script: """
                        curl -u github-credentials \
                        -s https://api.github.com/repos/vignesh-s19/vikki/pulls/${prNumber}/commits
                    """, returnStatus: false, returnStdout: true).trim()
                    
                    // Fetch PR creator's username
                    def prCreator = sh(script: """
                        curl -u github-credentials \
                        -s https://api.github.com/repos/vignesh-s19/vikki/pulls/${prNumber} | jq -r '.user.login'
                    """, returnStatus: false, returnStdout: true).trim()
                    
                    echo "Pull Request Details:"
                    echo "PR Number: ${prNumber}"
                    echo "PR Creator: ${prCreator}"
                    echo "Commit Message: ${commitMessage}"
                }
            }
        }
        
        stage('Send Email') {
            steps {
                script {
                    def prNumber = env.CHANGE_ID // Jenkins environment variable for PR number
                    def prCreator = sh(script: """
                        curl -u github-credentials \
                        -s https://api.github.com/repos/vignesh-s19/vikki/pulls/${prNumber} | jq -r '.user.login'
                    """, returnStatus: false, returnStdout: true).trim()
                    
                    def commitMessage = sh(script: """
                        curl -u github-credentials \
                        -s https://api.github.com/repos/vignesh-s19/vikki/pulls/${prNumber}/commits
                    """, returnStatus: false, returnStdout: true).trim()
                    
                    emailext body: "Pull Request Details:\n" +
                                   "PR Number: ${prNumber}\n" +
                                   "PR Creator: ${prCreator}\n" +
                                   "Commit Message: ${commitMessage}",
                             subject: "Pull Request Details for PR#${prNumber}",
                             to: 'tamilbecse139@gmail.com,vignesh-s19.sivasubramanian@quadgen.com',
                             mimeType: 'text/plain',
                             replyTo: 'tamilbecse139@gmail.com,vignesh-s19.sivasubramanian@quadgen.com'
                }
            }
        }
    }
}

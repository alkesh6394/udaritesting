node {
    def buildNo = currentBuild.number
    def commitId = sh(script: 'git rev-parse --short=6 HEAD', returnStdout: true).trim()
    def imageTag = "udharibazaar:${buildNo}+${commitId}"
    def jobName = env.JOB_NAME
    def buildUrl = env.BUILD_URL

    try {
        stage('Clean Workspace') {
            deleteDir()
        }
        stage('Build') {
                sh "docker build -t ${imageTag} ."
            }
        }

        stage('Push_docker_image') {
            sh "docker push ${imageTag}"
            echo "pushed ${imageTag}"
        }

        stage('Deploy') {
            if (env.BRANCH_NAME == 'master') {
                dir('public-api') { // Change to the desired directory
                    input message: "Do you want to continue", ok: "Yes, we should"
                    echo "apply ${imageTag}"
                }
            } else {
                echo "We cannot deploy the Docker image"
            }
        }
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        // Send Slack notification with the console URL and job name
        if (currentBuild.resultIsBetterOrEqualTo('SUCCESS')) {
            def message = "Your Jenkins Pipeline Run Successfully\nConsole URL: ${buildUrl}\nJob Name: ${jobName}"
            slackSend(channel: 'jenkins-pipeline', message: message, tokenCredentialId: 'C059M7SJL0K')
        } else {
            def message = "Your Jenkins Pipeline Did Not Run Successfully\nConsole URL: ${buildUrl}\nJob Name: ${jobName}"
            slackSend(channel: 'jenkins-pipeline', message: message, tokenCredentialId: 'C059M7SJL0K')
        }
    }
}

node {
    def jobName = env.JOB_NAME
    def buildUrl = env.BUILD_URL

    try {
        stage('Clean Workspace') {
            deleteDir()
        }

        stage('Checkout Code') {
            checkout scm
        }

        def buildNo = currentBuild.number
        def commitId = sh(script: 'git rev-parse --short=6 HEAD', returnStdout: true).trim()
        def imageTag = "udharibazaar:${buildNo}+${commitId}"

        stage('Build') {
            sh '''ls '''
        }

        stage('Push Docker Image') {
            if (env.BRANCH_NAME == 'master') {
                echo "Pushed ${imageTag}"
            } else {
                echo "We cannot push the Docker image"
            }
        }

        stage('Deploy') {
            if (env.BRANCH_NAME == 'master') {
                input message: "Do you want to continue", ok: "Yes, we should"
                echo "Apply ${imageTag}"
            } else {
                echo "We cannot deploy the Docker image"
            }
        }
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        if (currentBuild.resultIsBetterOrEqualTo('SUCCESS')) {
            def message = "Your Jenkins Pipeline Run Successfully\nConsole URL: ${buildUrl}\nJob Name: ${jobName}"
            slackSend(channel: 'jenkins-pipeline', message: message, tokenCredentialId: 'C059M7SJL0K')
        } else {
            def message = "Your Jenkins Pipeline Did Not Run Successfully\nConsole URL: ${buildUrl}\nJob Name: ${jobName}"
            slackSend(channel: 'jenkins-pipeline', message: message, tokenCredentialId: 'C059M7SJL0K')
        }
    }
}

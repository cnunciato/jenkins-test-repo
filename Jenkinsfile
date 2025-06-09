pipeline {
    agent any
    // This assumes you have Node.js available as a global
    // tool for all agents. In this case, we have the tool
    // with the exact name `Node 22.x` available for use
    // in our agents.
    tools {nodejs "Node 22.x"}
    environment {
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm ci'
            }
        }
        stage('Test') {
            steps {
                sh 'npm run test:all'
            }

            post {
                always {
                    // Use a script step to set the build description
                    // with Groovy.
                    // https://www.jenkins.io/doc/book/pipeline/syntax/#script
                    script {
                        // Executing our summary generator tool in the "always"
                        // condition ensures that it runs even regardless of
                        // the build status.
                        sh 'npm run summary'
                        // Long-form way of executing a shell script
                        // in a scripted step.
                        // https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script 
                        summary = sh(script: 'cat summary.html', returnStdout: true)
                        // buildDescription(summary)
                        currentBuild.description = summary
                    }
                    // Save the logs directory.
                    archiveArtifacts 'logs/**'
                    junit testResults: "test-report.xml"
                }
            }
        }
    }
}

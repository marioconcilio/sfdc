pipeline {
    agent { 
        docker { image 'salestrip/sfdx-cli' } 
    }
    environment {
        REPO_URL = "https://github.com/marioconcilio/sfdc/commit/"
    }
    stages {
        stage('Create package') {
            steps {
                script {
                    slack.notifyStarted()
                }
            }
        }
        stage('Deploy Dev') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'sfdc-dev1-key', variable: 'KEY')]) {
                        withCredentials([usernamePassword(credentialsId: 'sfdc-dev1-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                            sfdx.authDev(USERNAME, PASSWORD, KEY)
                        }
                    }

                    sh 'echo Hello world!'
                }
            }
        }
    }
    post {
        success {
            script {
                slack.notifySuccess()
            }
        }
        failure {
            script {
                slack.notifyFailure()
            }
        }
        unstable {
            script {
                slack.notifyUnstable()
            }
        }
        aborted {
            script {
                slack.notifyAborted()
            }
        }
    }
}
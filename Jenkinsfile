pipeline {
    agent { 
        docker { 
            image 'salesforce/salesforcedx'
            args '-e HOME=/tmp'
        } 
    }
    environment {
        SF_FORCE_SRC = 'force-app'
        SF_DEPLOY_SRC = 'deploy-app'
        SF_DEL_SRC = 'del-app'
        SF_DEPLOY_PKG = 'deploy.tar.gz'
        SF_DESTRUCTIVE_PKG = 'destructive.tar.gz'
        MANIFEST_FILE = 'MANIFEST.MF'
    }
    stages {
        stage('Create package') {
            steps {
                script {
                    slack.notifyStarted()
                    def gitPreviousSuccessfulCommit = git.checkout()
                    def manifest = git.getManifest(gitPreviousSuccessfulCommit)
                    writeFile file: "$MANIFEST_FILE", text: manifest

                    def lines = manifest.split("\n")
                    lines.each { filePath -> 
                        utils.buildWorkingDir(gitPreviousSuccessfulCommit, "$filePath")
                    }

                    utils.createPackage()
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: "$MANIFEST_FILE", onlyIfSuccessful: false, allowEmptyArchive: true
                }
            }
        }
        stage('Deploy to Dev') {
            when {
                branch 'develop'
                expression { utils.hasPackage() }
            }
            steps {
                script {
                    withCredentials([file(credentialsId: 'sfdc-dev1-key', variable: 'KEY')]) {
                        withCredentials([usernamePassword(credentialsId: 'sfdc-dev1-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                            sfdx.authDev(USERNAME, PASSWORD, KEY)
                        }
                    }
                    sfdx.deployDev()
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: "$SF_DEPLOY_PKG", onlyIfSuccessful: false, allowEmptyArchive: true
                }
            }
        }
        stage('Apply destructive package Dev') {
            when {
                branch 'develop'
                expression { utils.hasDestructivePackage() }
            }
            steps {
                script {
                    withCredentials([file(credentialsId: 'sfdc-dev1-key', variable: 'KEY')]) {
                        withCredentials([usernamePassword(credentialsId: 'sfdc-dev1-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                            sfdx.authDev(USERNAME, PASSWORD, KEY)
                        }
                    }
                    sfdx.applyDestructiveChangesDev()
                }
            }
            post {
                always {
                    rchiveArtifacts artifacts: "$SF_DESTRUCTIVE_PKG", onlyIfSuccessful: false, allowEmptyArchive: true
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
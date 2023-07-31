pipeline {
    agent any
    environment {
        USERNAME = 'rana-sahil-221'
        APITOKEN = '11e94fca26e992193c265ba90c56a8bba2'
        JENKINS_URL = 'http://localhost:8080/' // Replace with your Jenkins URL
        JOB_NAME = 'my-script' // Replace with your Jenkins job name
    }
    stages {
        stage('Cloning Repo') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[url: 'https://github.com/rana-sahil-221/demo-jenkins.git']]
                ])
            }
        }
        stage('Fetch Changelogs') {
            steps {
                script {
                    def buildNumber = currentBuild.number
                    def workspacePath = env.WORKSPACE
                    def changelogPath = "${workspacePath}/changelog.xml"

                    def changelogFile = new File(changelogPath)
                    if (changelogFile.exists()) {
                        def changelogContent = changelogFile.text
                        def changelogXml = new XmlSlurper().parseText(changelogContent)

                        if (changelogXml.entry) {
                            println "Changelogs for Build #${buildNumber}:"
                            changelogXml.entry.each { entry ->
                                println "Commit message: ${entry.msg.text()}"
                                println "Author: ${entry.author.fullName.text()}"
                                println "Commit ID: ${entry.commitId.text()}"
                                println "------------------------------------------"
                            }
                        } else {
                            println "No commits yet for Build #${buildNumber}."
                        }
                    } else {
                        println "No changelog.xml found in workspace for Build #${buildNumber}."
                    }
                }
            }
        }
    }
}

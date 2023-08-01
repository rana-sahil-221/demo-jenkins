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
    }
}
import groovy.json.JsonSlurper
import groovy.text.SimpleTemplateEngine

def jenkinsUrl = 'http://localhost:8080/' 
def jobName = 'my-script' 


def getChangelogForBuild(buildNumber) {
    def changelogUrl = "${jenkinsUrl}/job/${jobName}/${buildNumber}/api/json?tree=changeSet[items[comment]]"
    def response = new URL(changelogUrl).text
    def jsonSlurper = new JsonSlurper()
    def data = jsonSlurper.parseText(response)
    return data.changeSet.items.collect { it.comment ?: 'No commit' }
}

// Function to get the latest completed build number
def getLatestCompletedBuildNumber() {
    def jobUrl = "${jenkinsUrl}/job/${jobName}/api/json?tree=lastCompletedBuild[number]"
    def response = new URL(jobUrl).text
    def jsonSlurper = new JsonSlurper()
    def data = jsonSlurper.parseText(response)
    return data.lastCompletedBuild.number
}

// Fetch the latest completed build number for the specified job
def latestBuildNumber = getLatestCompletedBuildNumber()

// Fetch the changelog for the latest build
def changelog = getChangelogForBuild(latestBuildNumber)

// Print the commit message or "No commit" if the changelog is empty
if (changelog.isEmpty()) {
    println('No commit')
} else {
    println(changelog.join('\n'))
}

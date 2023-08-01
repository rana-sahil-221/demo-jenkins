import groovy.json.JsonSlurper

pipeline {
    agent any
    environment {
        USERNAME = 'rana-sahil-221'
        APITOKEN = '11e94fca26e992193c265ba90c56a8bba2'
        JENKINS_URL = 'http://localhost:8080/' 
        JOB_NAME = 'groovy'
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
    post {
        always {
            script {
                def buildNumber = currentBuild.number
                def changelogUrl = "${env.JENKINS_URL}job/${env.JOB_NAME}/${buildNumber}/api/json"
                def apiToken = "${USERNAME}:${APITOKEN}".bytes.encodeBase64().toString()

                def response = httpRequest(
                    url: changelogUrl,
                    customHeaders: [[name: 'Authorization', value: "Basic ${apiToken}"]],
                    httpMode: 'GET'
                )

                def jsonSlurper = new JsonSlurper()
                def buildInfo = jsonSlurper.parseText(response.getContent())

                if (buildInfo.changeSets && buildInfo.changeSets.length > 0) {
                    println "Changelogs for Build #${buildNumber}:"
                    buildInfo.changeSets.each { changeSet ->
                        changeSet.items.each { item ->
                            println "Commit message: ${item.msg}"
                            println "Author: ${item.author.fullName}"
                            println "Commit ID: ${item.commitId}"
                            println "------------------------------------------"
                        }
                    }
                } else {
                    println "No commits yet for Build #${buildNumber}."
                }
            }
        }
    }
}

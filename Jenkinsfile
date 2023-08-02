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

    stage('Fethcing Changelogs') {
          steps {
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
                def var = ''
                // Check if changeSets is not null and not empty
                if (buildInfo.changeSets && !buildInfo.changeSets.isEmpty()) {
                    println "Changelogs for Build #${buildNumber}:"
                    buildInfo.changeSets.each { changeSet ->
                        if (changeSet.items && !changeSet.items.isEmpty()) {
                            changeSet.items.each { item ->
                                println "Commit message: ${item.msg}"
                                  var = "${item.msg}"
                                  //println(var)
                            }
                        } else {
                            var = "No commits for Build #${buildNumber}."
                        }
                    }
                } else {
                    println "No commits for Build #${buildNumber}."
                }
            }
          }
    }
    }

    post {
        success {
            script {
               //def commitMsg = getChangelog()
                slackSend color: "good", message: "Deployment to K8 cluster done and artifact stored!", attachments: [[
                    color: 'good',
                    channel: '#general',
                    title: "BUILD DETAILS",
                    fields: [
                        [
                            title: "User",
                            value: "${env.BUILD_USER}",
                            short: true
                        ],
                        [
                            title: "BUILD NUMBER",
                            value: "${currentBuild.number}",
                            short: true
                        ],
                        [
                            title: "Changelog",
                            value: $var,
                            color: "good"
                        ],
                        [
                            title: "JOB URL",
                            value: "${env.JOB_URL}",
                            short: true
                        ]
                    ]
                  ]]   
            
             }
        }
        
      failure {
          slackSend (color: "danger", message: "Deployment to K8 cluster failed!", attachments: [[
            color: 'danger',
            title: "BUILD DETAILS",
            fields: [[
              title: "User",
              value: "${env.BUILD_USER}",
              short: true
            ],
            [
              title: "BUILD NUMBER",
              value: "${currentBuild.number}",
              short: true
            ],
            [
              title: "JOB URL",
              value: "${env.JOB_URL}",
              short: true
            ]]
          ]]
        )
      }
    }
}

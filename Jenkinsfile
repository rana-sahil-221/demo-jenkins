import groovy.json.JsonSlurper
def commit = []
def varmsg = ''
def commitMsg = ''
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
                
                // Check if changeSets is not null and not empty
                if (buildInfo.changeSets && !buildInfo.changeSets.isEmpty()) {
                    println "Changelogs for Build #${buildNumber}:"
                    buildInfo.changeSets.each { changeSet ->
                        if (changeSet.items && !changeSet.items.isEmpty()) {
                            changeSet.items.each { item ->
                                //println "Commit message: ${item.msg}"
                                  varmsg += "${item.msg}" + "\n"
                                  println(varmsg)
                                  //var = "${item.msg}"
                                   //commit.add(item.msg)
                                  //println(var)
                                 
                            }
                        } else {
                            //varmsg = "No commits for Build #${buildNumber}."
                        }
                    }
                               //commitMsg = commit.join("\n")
                                
                } else {
                     varmsg = "No commits for Build #${buildNumber}."
                     println(varmsg)
                }
            }
          }
    }
    }

    post {
        success {
            script {
               //def commitMsg = getChangelog()
                def changelogFile = new File("${WORKSPACE}/changelog_commits.txt")
                                changelogFile.write(varmsg)
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
                            title: "CHANGELOGS",
                            value: "\n${varmsg}",
                            color: "good"
                        ],
                        [
                            title: "JOB URL",
                            value: "${env.JOB_URL}",
                            short: true
                        ]
                    ]
                  ]]   
           withCredentials([string(credentialsId: 'slackToken', variable: 'SLACK_TOKEN')]) {
           sh "curl -F \"file=@${WORKSPACE}/changelog_commits.txt\" " +
               "-F \"channels=#general\" " +
               "-F \"initial_comment=Changelog Commits for Build #${currentBuild.number}\" " +
               "-F \"filetype=text\" " +
               "https://slack.com/api/files.upload " +
               "-H \"Authorization: Bearer ${SLACK_TOKEN}\""
           }
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
              title: "CHANGELOGS",
              value: "${varmsg}",
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

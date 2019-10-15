#!/usr/bin/env bash
 pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '1'))
  }
  stages {
    stage('Deploy Environment via Jenkins function') {
          steps {
            script {
              assert lagoonDeployEnvironment('algmprivsecops', 'umami-demo' , 'master')
            }
          }
        }
    stage('Deploy Environment via Bash Library') {
          steps {
            sshagent(['algmprivsecops']) {
              sh '''
              . ./lagoon_bash_lib.sh
              lagoon_deploy umami-demo master
              '''
            }
          }
        }
    }
}

boolean lagoonDeployEnvironment(String sshAgentName, String queryProject, String queryEnvironment, String lagoonEndpoint = 'https://api.lagoon.amazeeio.cloud/graphql', lagoonSshEndpoint = 'ssh.lagoon.amazeeio.cloud', lagoonSshPort = '32222') {
    sshagent([sshAgentName]) {
    script {
            sshTokenCommand = '''
            set +x
            ssh -p %s -o LogLevel=ERROR -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -t lagoon@%s token
            set -x
            '''

            sshToken = sh(returnStdout: true, script: String.format(sshTokenCommand, lagoonSshPort, lagoonSshEndpoint)).trim()
            queryTemplate = '{ "query": "mutation { deployEnvironmentLatest(input: { environment: { name: \\"%s\\" project: { name: \\"%s\\" } } }) }" }'
            query = String.format(queryTemplate, queryEnvironment, queryProject)
            queryHeader = String.format("Authorization: Bearer %s", sshToken);
            withEnv(['AUTHHEADER=' + queryHeader]) {
                curlTemplate = '''
                set +x
                curl -X POST -H 'Content-Type: application/json' -H "$AUTHHEADER" -d '%s' %s 2>/dev/null
                set -x
                '''
                curlCommand = String.format(curlTemplate, query, lagoonEndpoint)
                queryResult = sh(returnStdout: true, script: curlCommand)
                return queryResult.contains("success");
            }
        }
   }
}
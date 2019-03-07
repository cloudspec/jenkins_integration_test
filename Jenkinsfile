import java.text.SimpleDateFormat

properties([
  disableConcurrentBuilds(),
  parameters([
  // Parameters for git/checkout class
  //  string(
  //    defaultValue: '',
  //    description: "SHA of the commit you want to deploy",
  //    name: 'SHA'
  //  ),
    choice(
      name: 'ENV',
      description: "The enviroment to deploy to.",
      choices: [
        'dev',
        'test',
        'stage',
        'prod'
      ].join('\n')
    ),
    booleanParam(
      defaultValue: true, 
      description: 'Should we tag and push this?', 
      name: 'GIT_TAG_AND_PUSH'
    ),
    string(
      defaultValue: '', 
      description: 'Git tag to push.', 
      name: 'GIT_TAG'
    )
  ]),
])

node("master") {

  def AWS_DEFAULT_REGION = "us-east-1"
  def PWD = pwd()
  def dateFormat = new SimpleDateFormat("yyyy.MM.dd")
  def now = new Date()

  // slack channel for notifications
  def channel = '#cloudspec-builds'

  // get current SHA
  def scmVars = checkout scm
  def SHA = scmVars.GIT_COMMIT

  // set current build name
  currentBuild.displayName = "#${currentBuild.number}: test_repo $ENV"

  if ( ENV == '' ) {
    ENV = 'dev'
  }

  LS = sh (
    returnStdout: true, 
    script: "ls -l ${PWD}"
  )


  stage('Build') {
    ansiColor('xterm') {
      try {
        echo "Dumping 'currentBuild' object."
        println currentBuild.dump()
        echo "AWS_DEFAULT_REGION: ${AWS_DEFAULT_REGION}"
        echo "SHA: ${SHA}"
        echo "LS: ${LS}"
        //  sh "date xx"

        // git push
        if ( GIT_TAG_AND_PUSH == true ) {

          withCredentials([
            sshUserPrivateKey(
              credentialsId: 'cloudspec_test', 
              keyFileVariable: 'SSH_KEY'
            )
          ]) {
            sh("git tag $GIT_TAG")
            sh("git push --tags")
          }
        }

        slackSend color: 'good', channel: channel, message: "Build *${currentBuild.currentResult}*."
      } catch (e) {
        currentBuild.result = "FAILED"
        slackSend color: 'danger', channel: channel, message: "Build *${currentBuild.currentResult}*. Error: ${e}"
        throw e
      }  
    }
  }
}

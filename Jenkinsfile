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
    )
  ])
])

node("master") {

  def AWS_DEFAULT_REGION = "us-east-1"
  def PWD = pwd()
  def dateFormat = new SimpleDateFormat("yyyy.MM.dd")
  def now = new Date()

  // slack channel for notifications
  def channel = '#cal-ready-builds'

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

  // slackSend color: 'good', channel: channel, message: "Starting $ENV build."

  stage('Build') {
    ansiColor('xterm') {
      try {
        echo "AWS_DEFAULT_REGION: ${AWS_DEFAULT_REGION}"
        echo "SHA: ${SHA}"
        echo "LS: ${LS}"
      } catch (e) {
        currentBuild.result = "FAILED"
        throw e
        // slackSend color: 'danger', channel: channel, message: "Stage: Build *${currentBuild.currentResult}*. Error: ${e}"
      }  
    }
  }
}

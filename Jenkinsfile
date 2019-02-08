import java.text.SimpleDateFormat

properties([
  disableConcurrentBuilds(),
  parameters([
    // Parameters for git/checkout class
    string(
      defaultValue: '',
      description: "SHA of the commit you want to deploy",
      name: 'SHA'
    ),
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

  // if ( SHA == '' ) {
  //   SHA = checkout(scm).GIT_COMMIT
  // }

  if ( ENV == '' ) {
    ENV = 'dev'
  }

  AWS_DEFAULT_REGION = "us-east-1"

  def PWD = pwd()
  def dateFormat = new SimpleDateFormat("yyyy.MM.dd")
  def now = new Date()

  // slack channel for notifications
  def channel = '#cal-ready-builds'

  currentBuild.displayName = "#${currentBuild.number}: test_repo $ENV"

  // slackSend color: 'good', channel: channel, message: "Starting $ENV build."

  stage('Build') {
    ansiColor('xterm') {
      try {
        echo "${AWS_DEFAULT_REGION}"
      } catch (e) {
        currentBuild.result = "FAILED"
        throw e
        // slackSend color: 'danger', channel: channel, message: "Stage: Build *${currentBuild.currentResult}*. Error: ${e}"
      }  
    }
  }
}

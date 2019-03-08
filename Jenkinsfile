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

  deleteDir()
  def PWD = pwd()
  def dateFormat = new SimpleDateFormat("yyyy.MM.dd")
  def now = new Date()
  def scmVars = checkout scm
  def SHA = scmVars.GIT_COMMIT
  def scmUrl = scm.getUserRemoteConfigs()[0].getUrl()

  currentBuild.displayName = "#${currentBuild.number}: git push test $ENV"

  LS1 = sh (
    returnStdout: true, 
    script: "ls -ald ${PWD}"
  )

  LS2 = sh (
    returnStdout: true, 
    script: "ls -ald ${PWD}/.*"
  )


  stage('Build') {
    ansiColor('xterm') {
      try {
        echo "Dumping 'currentBuild' object."
        println currentBuild.dump()
        echo "SHA: ${SHA}"
        echo "LS1: ${LS1}"
        echo "LS2: ${LS2}"
        echo "scmUrl: ${scmUrl}"

        def credentialsId = 'cloudspec_test'

        sh "git tag ${GIT_TAG}"

        withCredentials([
          sshUserPrivateKey(
            credentialsId: credentialsId,
            keyFileVariable: 'SSH_KEY'
          )
        ]) {

          sh "GIT_SSH_COMMAND='ssh -i ${SSH_KEY}' git push --tags"

        }

      } catch (e) {
        currentBuild.result = "FAILED"
        throw e
      }  
    }
  }
}

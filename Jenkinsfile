node('fixed_slave') {
  stage('SCM') 
  checkout scm
  stage('compile')
  sh "mvn install package"
  sh "mvn cobertura:cobertura"
  stage('SonarQube analysis') 
  def sonarqubeScannerHome = tool name: 'sonarqube scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
  def sonarQubeUrl = "http://192.168.26.132:9000"
  sh "${sonarqubeScannerHome}/bin/sonar-runner -e -Dsonar.host.url=${sonarQubeUrl}"  
  stage('Set Github Status')
  setBuildStatus("Build complete", "SUCCESS") 
}

def setBuildStatus(message,state){
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/theocui/maven"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ])
}

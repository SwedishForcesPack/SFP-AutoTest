node ('arma') {
  stage('Checkout from Git') {
    checkout scm
  }

  stage('Download latest SFP') {
    bat 'gradlew downloadLatestSFP'
  }

  stage('Run autotest') {
    bat 'gradlew runAutotest'
  }

  stage('Copy Log File') {
    bat 'gradlew copyLogFile'
  }

  stage('Archive Log File') {
  	archiveArtifacts artifacts: 'logs/*.rpt'
  }
}

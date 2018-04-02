String nightlyBuild = BRANCH_NAME == 'master' ? '0 4 * * *' : ''

pipeline {
    agent none

    triggers {
        cron(nightlyBuild)
    }

    options {
        skipDefaultCheckout()
    }

    environment {
        DISCORD_WEBHOOK_URL = credentials('SFP_DISCORD_WEBHOOK_URL')
        STEAM_USERNAME = credentials('SFP_AUTOTEST_STEAM_USERNAME')
        STEAM_PASSWORD = credentials('SFP_AUTOTEST_STEAM_PASSWORD')
    }

    stages {
        stage('Build') {
            parallel {
                stage('SFP') {
                    agent {
                        label 'arma'
                    }

                    steps {
                        runForMod('sfp')
                    }
                }

                stage('FFP') {
                    agent {
                        label 'arma'
                    }

                    steps {
                        runForMod('ffp')
                    }
                }

                stage('SFP Objects') {
                    agent {
                        label 'arma'
                    }

                    steps {
                        runForMod('sfp_objects')
                    }
                }

                stage('SFP Roso') {
                    agent {
                        label 'arma'
                    }

                    steps {
                        runForMod('sfp_roso')
                    }
                }

                stage('SFP Sturko') {
                    agent {
                        label 'arma'
                    }

                    steps {
                        runForMod('sfp_sturko')
                    }
                }

                stage('SFP Wamako') {
                    agent {
                        label 'arma'
                    }

                    steps {
                        runForMod('sfp_wamako')
                    }
                }
            }
        }
    }

    post {
        always {
            sendNotificationToDiscord()
        }
    }
}

void sendNotificationToDiscord() {
    def description = "**Log:**\n${BUILD_URL}console\n**Artifacts:**\n${BUILD_URL}artifact/"
    def successful = currentBuild.resultIsBetterOrEqualTo('SUCCESS')
    def title = JOB_NAME

    if (env.CHANGE_TITLE) {
        title += " - ${env.CHANGE_TITLE}"
    }

    if (env.CHANGE_URL) {
        description += "\n**GitHub:** ${env.CHANGE_URL}"
    }

    discordSend description: description, link: BUILD_URL, title: title, successful: successful, webhookURL: DISCORD_WEBHOOK_URL
}

def runForMod(mod) {
    checkout scm
    bat "gradlew ${mod}:downloadMods"
    bat "gradlew ${mod}:runAutotest"
    bat "gradlew ${mod}:moveLogFile"
    archiveArtifacts artifacts: "${mod}/logs/*.rpt"
}

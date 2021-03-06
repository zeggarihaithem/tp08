pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'gradle build'
        bat 'gradle javadoc'
        archiveArtifacts 'build/libs/**/*.jar'
        archiveArtifacts 'build/docs/**'
        junit 'build/test-results/test/*.xml'
      }
    }

    stage('Mail Notification') {
      steps {
        mail(subject: 'subject', body: 'test', from: 'ga_ourdjini@esi.dz', to: 'gh_zeggari@esi.dz', replyTo: 'gi_berkane@esi.dz')
      }
    }

    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            withSonarQubeEnv('sonar') {
              bat 'gradle sonarqube'
            }

            waitForQualityGate true
          }
        }

        stage('Test Reporting') {
          steps {
            jacoco()
          }
        }

      }
    }

    stage('Deployment') {
      when {
        branch 'master'
      }
      steps {
        bat 'gradle uploadArchives'
      }
    }

    stage('Slack Notification') {
      when {
        branch 'master'
      }
      steps {
        slackSend(baseUrl: 'https://hooks.slack.com/services/', token: 'TRQBWN22C/BT4D88D17/rsQ1k8TqVNhyxotCYNLDHFTI', username: 'ga_ourdjini', teamDomain: 'esi-oo64659', channel: '#tp6', color: '#ff0000', failOnError: true, message: 'build sucessfull', sendAsText: true)
      }
    }

  }
}

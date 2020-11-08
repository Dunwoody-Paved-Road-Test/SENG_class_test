pipeline {
    agent any

    stages {
        stage('copy to nginx') {
            steps {
                script {
                    def remote = [:]
                    remote.name = "vagrant"
                    remote.host = "192.168.56.21"
                    remote.allowAnyHosts = true
                    remote.failOnError = true

                    withCredentials([usernamePassword(credentialsId: 'vagrant', passwordVariable: 'password', usernameVariable: 'username')]) {
                    remote.user = username
                    remote.password = password
                    }

                    sshPut remote: remote, from: './', into: '/var/www/data/static-web'
                }
            }
        }
        stage('write changlog'){
            steps{
                script{
                def changelogString = gitChangelog returnType: 'STRING',
                    template: """{{#commits}}{{messageTitle}},{{authorEmailAddress}},{{commitTime}}
                    {{/commits}}"""
                writeFile file: 'ChangeLog.txt', text: changelogString
                }
            }
        }
        stage('email notifications'){
            steps{
                emailext body: 'test', recipientProviders: [requestor()], subject: 'commit', to: 'dunwoodypavedroadtest@gmail.com'
            }
        }
    }
}
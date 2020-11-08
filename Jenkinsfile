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
                    // def changelogString = gitChangelog returnType: 'STRING',
                    //     template: """{{#commits}}{{messageTitle}},{{authorEmailAddress}},{{commitTime}}
                    //     {{/commits}}"""
                    // writeFile file: 'ChangeLog.txt', text: changelogString
                    // def contents = readFile 'ChangeLog.txt'
                    // print contents
                    def changelogContext = gitChangelog returnType: 'CONTEXT'
                    def timestamp = changelogContext.commits.commitTime
                    for (int i = 0; i < timestamp.size(); i++) {
                        def time = timestamp[i]
                        def hourMin = time.split(':')
                        print hourMin
                    }
                    // changelogContext.each {
                    //     print it.commits.commitTime
                    // }
                    // print changelogContext.commits[0].authorEmailAddress
                }
            }
        }
        // stage('email notifications'){
        //     steps{
        //         emailext body: 'test', recipientProviders: [requestor()], subject: 'commit', to: 'dunwoodypavedroadtest@gmail.com'
        //     }
        // }
    }
}
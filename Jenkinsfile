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
                    def emailList = changelogContext.commits.authorEmailAddress
                    def check1
                    def check2
                    for (int i = 0; i < timestamp.size(); i++) {
                        check2 = check1
                        def time = timestamp[i]
                        //print time
                        def hourMin = time.split(':')
                        check1 = hourMin[0] + hourMin[1]
                        print check1
                        if (check1 == check2) {
                            echo 'Match!'
                        }
                        if (i == 0) {
                            echo 'first round'
                        }
                        else {
                            break
                        }
                        
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
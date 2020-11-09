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
        // stage('Validate HTML') {
        //     steps{
        //         script{
        //             httpRequest httpMode: 'POST', requestBody: '{ [ { "contents": contents }, { "email": changelogContext.commits[i].authorEmailAddress }, ] }', responseHandle: 'NONE', url: 'url', wrapAsMultipart: false
        //         }
        //     }
        // }
        stage('Email Notifications'){
            steps{
                script{
                    // def contents = readFile 'ChangeLog.txt'
                    // print contents
                    def changelogContext = gitChangelog returnType: 'CONTEXT'
                    def timestamp = changelogContext.commits.commitTime
                    def emailList = changelogContext.commits.authorEmailAddress
                    def emailBody
                    def check1
                    def check2
                    for (int i = 0; i < timestamp.size(); i++) {
                        check2 = check1
                        def time = timestamp[i]
                        def userName = emailList[i].split('@')
                        userName = userName[0]
                        def hourMin = time.split(':')
                        check1 = hourMin[0] + hourMin[1]
                        // if the previous timestamp has the same hour and minute
                        // this is if Jenkins detects more than one new commit during a scan. For example, two users commit 
                        // somehting at the same time, or within one minute of each other
                        if (check1 == check2) {
                            emailBody = 'Your static html is now available at IP/static-web/' + userName
                            emailext body: emailBody, subject: 'Paved-Road Auto Notification', to: emailList[i]
                        }
                        // if the first iteration of the loop
                        if (i == 0) {
                            emailBody = 'Your static html is now available at IP/static-web/' + userName
                            emailext body: emailBody, subject: 'Paved-Road Auto Notification', to: emailList[i]
                            // validate html
                            def htmlFiles = findFiles excludes: '', glob: userName + '/'
                            htmlFiles.each {
                                def html = readFile it
                                print html
                            }
                            //def contents = readFile '$userName/'
                        }
                        else {
                            break
                        }
                    }
                }
            }
        }
        stage('Write Changelog'){
            steps{
                script{
                    def changelogString = gitChangelog returnType: 'STRING',
                        template: """{{#commits}}{{messageTitle}},{{authorEmailAddress}},{{commitTime}}
                        {{/commits}}"""
                    writeFile file: 'ChangeLog.txt', text: changelogString
                }
            }
        }
    }
}
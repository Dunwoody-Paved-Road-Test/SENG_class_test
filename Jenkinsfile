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

                    def author = ""
                    def changeSet = currentBuild.rawBuild.changeSets               
                    for (int i = 0; i < changeSet.size(); i++) 
                    {
                    def entries = changeSet[i].items;
                    for (int i = 0; i < changeSet.size(); i++) 
                                {
                                        def entries = changeSet[i].items;
                                        def entry = entries[0]
                                        author += "${entry.author}"
                                } 
                    }
                    print author;
                }
            }
        }
    }
}
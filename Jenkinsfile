pipeline {
    agent any

    stages {
        stage('copy to nginx') {
            steps {
                remote = [:]
                remote.name = "vagrant"
                remote.host = "192.168.56.21"
                remote.allowAnyHosts = true
                remote.failOnError = true

                withCredentials([usernamePassword(credentialsId: 'vagrant', passwordVariable: 'password', usernameVariable: 'username')]) {
                remote.user = username
                remote.password = password
                }

                sshPut remote: remote, from: '${workspace}', into: '/var/www/data/static-web/'

                //withCredentials([usernamePassword(credentialsId: 'vagrant', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                // available as an env variable, but will be masked if you try to print it out any which way
                // note: single quotes prevent Groovy interpolation; expansion is by Bourne Shell, which is what you want
                //sh 'echo $PASSWORD'
                // or inside double quotes for string interpolation
                //echo "username is $USERNAME"
                }
            }
        }
    }
}
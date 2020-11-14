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
        stage('Email Notifications'){
            steps{
                script{
                    // format the workspace for the url 
                    def workspace = "$WORKSPACE"
                    workspace = workspace.split('/')
                    workspace = workspace[-1]
                    (userMap, userDirectories) = getCommitFiles() // see getCommitFiles function below

                    def fileContents
                    def htmlFiles
                    // send mail
                    for (int a = 0; a < userDirectories.size(); a++){
                        def emailBody = """Your static html is now available at:\n"""
                        // get the filepaths for the user
                        def user = userDirectories[a]
                        def paths = userMap[user]
                        for (int b = 0; b < paths.size(); b++) {
                            def path = paths[b]
                            emailBody = emailBody + """http://98.240.222.112:49160/static-web/${workspace}/${path}/ \n"""
                        // create json request body
                            fileContents = readFile path
                            def filename = path.split('/')
                            filename = filename[-1]
                            if (b == paths.size() - 1) {
                                htmlFiles = htmlFiles + '{"filename": "${filename}", "fileData":"${fileContents}"}'
                            }
                            else {
                                htmlFiles = htmlFiles + '{"filename": "${filename}", "fileData":"${fileContents}"}' + ','
                            }
                            
                        }
                        def jsonBody = """
                            {
                                "users": [
                                    {
                                        "htmlFiles": [ ${htmlFiles} ],
                                        "userEmail": "${user}@dunwoody.edu"
                                    }
                                ]
                            }
                            """
                        // validate html
                        def response = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: jsonBody, responseHandle: 'NONE', url: '192.168.56.25:8080/api/validateHtml', wrapAsMultipart: false
                        emailBody = emailBody + response.content
                        emailext body: emailBody, subject: 'Paved-Road Auto Notification', to: user
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
@NonCPS
def getCommitFiles() {
    // get the changed files in the current build
    def userDirectories = []
    def userMap = [:]
    def changeLogSets = currentBuild.changeSets
    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            //echo "${entry.commitId} by ${entry.author} on ${new Date(entry.timestamp)}: ${entry.msg}"
            def files = new ArrayList(entry.affectedFiles)
            for (int k = 0; k < files.size(); k++) {
                def file = files[k]
                echo "File Changed: ${file.path}"
                // get the user's directory and the changed file
                def filepath = "${file.path}"
                def userDir = filepath.split('/')
                userDir = userDir[0]
                
                // Map filepaths to the associated user
                if (userDir == 'Jenkinsfile') {
                    echo "skip Jenkinsfile"
                    continue
                }
                if (userMap.containsKey(userDir)) {
                    echo "user already exists"
                    userMap[userDir].add(filepath)
                    print userMap
                }
                else {
                    echo "new user, adding key"
                    userMap[userDir] = []
                    userMap[userDir].add(filepath)
                    print userMap
                    userDirectories = userDirectories + [userDir]
                    print userDirectories
                }
            }
        }
    }
    return [userMap, userDirectories]
}
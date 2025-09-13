pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                git(
                    branch: 'prod',
                    credentialsId: 'git-cred',
                    url: 'https://github.com/Maanik2003/Boardgame.git'
                )
            }
        }
    }
}

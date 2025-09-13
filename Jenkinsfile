pipeline {
    agent any

    tools {
        maven 'maven3'   // Yeh wahi name hona chahiye jo Jenkins Global Tool Config me diya hai
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'prod',
                    credentialsId: 'git-cred',
                    url: 'https://github.com/Maanik2003/Boardgame.git'
            }
        }

        stage('Maven Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Maven Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}

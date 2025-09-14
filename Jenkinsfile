pipeline {
    agent any

    tools {
        maven 'maven3'   // Jenkins Global Tool Config must have this name
    }

    environment {
        // Set Git credentials ID for push
        GIT_CREDENTIALS = 'git-cred'
        GIT_BRANCH = 'prod'
        REPO_URL = 'https://github.com/Maanik2003/Boardgame.git'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: "${GIT_BRANCH}",
                    credentialsId: "${GIT_CREDENTIALS}",
                    url: "${REPO_URL}"
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

        stage('Verify Trivy Installation') {
            steps {
                echo 'Checking Trivy installation on Jenkins agent...'
                sh 'which trivy'
                sh 'trivy --version'
            }
        }

        stage('File System Scan by Trivy') {
            steps {
                echo 'Trivy Scan Started'
                // Output both to console and file
                sh 'trivy fs --format table . | tee trivy-filescanproject-output.txt'
            }
        }

        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    // Use tool installed in PATH or configured in Jenkins
                    sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=BoardGame \
                        -Dsonar.projectName=BoardGame \
                        -Dsonar.java.binaries=. \
                        -Dsonar.exclusions=**/trivy-filescanproject-output.txt
                    '''
                }
            }
        }


    post {
        always {
            // Archive Trivy report so it's visible in Jenkins UI
            archiveArtifacts artifacts: 'trivy-filescanproject-output.txt', allowEmptyArchive: true
        }
    }
}

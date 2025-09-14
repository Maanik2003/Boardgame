pipeline {
    agent any

    tools {
        maven 'maven3'   // Jenkins Global Tool Config me yahi name hona chahiye
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
                withSonarQubeEnv('sonar') {   // 'sonar' must match Jenkins SonarQube server config
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=BoardGame \
                        -Dsonar.projectName=BoardGame \
                        -Dsonar.java.binaries=. \
                        -Dsonar.exclusions=**/trivy-filescanproject-output.txt
                    '''
                }
            }
        }
    }

    post {
        always {
            // Archive Trivy report so it is visible in Jenkins UI
            archiveArtifacts artifacts: 'trivy-filescanproject-output.txt', allowEmptyArchive: true
        }
    }
}

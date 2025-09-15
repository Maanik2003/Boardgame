pipeline {
    agent any

    tools {
        maven 'Maven'   // Your Jenkins Maven installation name
        jdk 'JDK11'     // Your Jenkins JDK installation name
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Declarative: Tool Install') {
            steps {
                echo "Using Jenkins configured tools"
            }
        }

        stage('Git Checkout') {
            steps {
                sh 'git checkout prod || git checkout -b prod'
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

        stage('File System Scan by Trivy') {
            steps {
                sh 'trivy fs --exit-code 0 --severity HIGH,CRITICAL . > trivy-filescanproject-output.txt'
                archiveArtifacts artifacts: 'trivy-filescanproject-output.txt', fingerprint: true
            }
        }

        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=BoardGame \
                          -Dsonar.projectName=BoardGame \
                          -Dsonar.java.binaries=target/classes \
                          -Dsonar.exclusions=**/trivy-filescanproject-output.txt
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Maven Package') {
            steps {
                sh 'mvn package -DskipTests'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        always {
            echo "Pipeline finished"
        }
    }
}

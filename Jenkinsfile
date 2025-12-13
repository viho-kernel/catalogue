pipeline {
    agent {
        node {
            label 'Agent-1'
        }
    }

    environment {
        appVersion = ''
    }

    options {
        timeout(time: 1, unit: 'HOURS')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }

    stages {
        stage('version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    env.appVersion = packageJson.version   // store in env so later stages can use it
                    echo "Application Version: ${env.appVersion}"
                }
            }
        }

        stage('build') {
            steps {
                sh """
                echo "Building application"
                npm install
                npm run build
                """
            }
        }

        stage('zip') {
            steps {
                sh """
                echo "Zipping build artifacts"
                zip -q -r catalogue-${env.appVersion}.zip ./* -x .git/* -x node_modules/* -x *.zip
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline completed."
            deleteDir()
        }
    }
}

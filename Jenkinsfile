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
            stage ('version'){
                steps {
                    def packageJson = readJSON file: 'package.json'
                    pacageVersion = packageJson.version
                    echo "Application Version: ${appVersion}"
            }
        }
        stage ('build'){
                steps {
                    sh """
                    echo "Building application"
                    npm install
                    npm run build
                    """
                }
            }
        stage ('zip') {
            steps {
                sh """
                echo "Zipping build artifacts"
                zip -q -r catalogue-${appVersion}.zip ./* -x .git/* -x node_modules/* -x *.zip
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
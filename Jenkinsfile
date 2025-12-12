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
        }


        stages {
            stage('version') {
                steps {
                    def packageJson = readJSON file: 'package.json'
                    packageVersion = packageJson.version

                    echo "App Version: ${packageVersion}"
                }
            }

            stage('Install Dependencies') {
                steps {
                    sh 'npm install'
                }
            }

            stage('Deploy') {
                steps {
                    sh """
                    ls -la
                    npm run build
                    """
                }
            }

            stage('zip') {
                steps {
                    sh 'zip -q -r catalogue.zip ./* -x *.git* -x *.zip*'

                }
            }
        }

        post {
            always {
                echo "pipeline is successful :)"
                deleteDir()
            }
        }
}
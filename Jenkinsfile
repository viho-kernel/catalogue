pipeline {
    agent {
        node {
            label 'Agent-1'
        }
    }

    environment {
        appVersion = ''
        url = '172.31.76.23:8081'
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

        stage('Install Dependencies') {
            steps {
                sh """
                echo "Installing npm dependencies 📩"
                npm install 
                echo"✅ Dependencies installed"

                """
            }
        }

        stage('build') {
            steps {
                sh """
                ls -la
                zip -q -r catalogue-${env.appVersion}.zip ./* -x *.zip -x ".git"
                """
            }
        }

        stage('Artifact Uploader') {
            steps {
        nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: '${url}',
        groupId: 'roboshop',
        version: env.appVersion,
        repository: 'catalogue',
        credentialsId: 'nexus-auth',
        artifacts: [
            [artifactId: catalogue,
             classifier: '',
             file: 'catalogue-' + env.appVersion + '.zip',
             type: '.zip']
        ]
     )
            }
        }

        stage ('Deploy') {
            steps {
                sh """
                echo "Deploying catalogue-${env.appVersion}.zip to server..."
                echo "✅ Deployment completed."
                sleep 10
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline completed."
            deleteDir()
        }

        success {
            echo "Pipeline succeeded!"
        }

        failure {
            echo "Pipeline failed!"
        }
    }
}

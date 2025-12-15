pipeline {
    agent {
        label 'Agent-1'
    }

    environment {
        appVersion = ''
        url = '100.31.46.163:8081'
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
                    env.appVersion = packageJson.version
                    echo "Application Version: ${env.appVersion}"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh """
                echo "Installing npm dependencies 📩"
                npm install 
                echo "✅ Dependencies installed"
                """
            }
        }

        stage('build') {
            steps {
                sh """
                ls -la
                zip -q -r catalogue-${env.appVersion}.zip ./* -x *.zip -x ".git"
                ls -ltr
                """
            }
        }

        // stage('Artifact Uploader') {
        //     steps {
        //         nexusArtifactUploader(
        //             nexusVersion: 'nexus3',
        //             protocol: 'http',
        //             nexusUrl: "${env.url}",
        //             groupId: 'com.roboshop',
        //             version: "${env.appVersion}",
        //             repository: 'catalogue',
        //             credentialsId: 'nexus-auth',
        //             artifacts: [
        //                 [artifactId: 'catalogue',
        //                  classifier: '',
        //                  file: "catalogue-${env.appVersion}.zip",
        //                  type: 'zip']
        //             ]
        //         )
        //     }
        // }

        stage ('Deploy') {
            steps {
                sh """
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

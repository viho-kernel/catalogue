pipeline {
    agent {
        node {
        label 'Agent-1'
        }
    }

    tools {
        git 'default'
    }

    environment {
        appVersion = ' '
        url = '100.31.46.163:8081'
    }

    options {
        timeout(time: 1, unit: 'HOURS')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
parameters {
        // string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')

        // text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')

        booleanParam(name: 'Deploy', defaultValue: false, description: 'Toggle this value')

        // choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

        // password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    }

    stages {
        stage('version') {
    steps {
         script {
                    def packageJson = readJSON file: 'package.json'
                    packageVersion = packageJson.version
                    echo "application version: $packageVersion"
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
                zip -q -r catalogue.zip ./* -x ".git" -x "*.zip"
                ls -ltr
                """
            }
        }

        stage('Artifact Uploader') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${url}",
                    groupId: 'com.roboshop',
                    version: "${appVersion}",
                    repository: 'catalogue',
                    credentialsId: 'nexus-auth',
                    artifacts: [
                        [artifactId: 'catalogue',
                         classifier: '',
                         file:'catalogue.zip',
                         type: 'zip']
                    ]
                )
            }
        }

        stage ('Deploy') {
            steps {
                build job: 'catalogue-deploy', wait: true,
                parameters: [
                    string(name: 'version', value: "$appVersion"),
                    string(name: 'environment', value: "dev")
                ]
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

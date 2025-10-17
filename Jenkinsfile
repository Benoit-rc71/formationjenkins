pipeline {
    agent any
    environment {
        URL_GIT = 'https://github.com/Benoit-rc71/formationjenkins.git'
        CREDENTIALS_ID = 'token-github'
    }

    parameters {
        // string(name: "version", description: "Version déployée")
        booleanParam(name: "mineur", description: "Version à déployer")
        booleanParam(name: "major", description: "Version à déployer")
        booleanParam(name: "patch", description: "Version à déployer")
        booleanParam(name: "snapshot", description: "Version à déployer")
        choice(name: "environment", choices: ["Test", "Préprod", "Prod"], description: "Environnement sur lequel déployer le livrable")
    }

    stages {
        stage('Cloner le répertoire') {
            steps {
                //git branch: 'master', url: 'https://github.com/Benoit-rc71/jenkins.git'
                checkout([
                    $class: 'GitSCM',
                    //source: ,
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[
                        url: "${URL_GIT}",
                        credentialsId: "${CREDENTIALS_ID}"                      
                    ]]
                ])
                echo "Répertoire cloné"
                sh "printenv"
            }
        }
         stage('Build Project') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package spring-boot:repackage -Dmaven.test.skip=true'
            }
         }
         stage('Test') {
            steps {
                sh 'ls -al'
                //sh 'cat servers.csv'
                script {
                    def lines = readFile('servers.csv').split("\n")
                    lines.eachWithIndex { line, index ->
                    echo "Ligne ${index + 1} : ${line}"
                    }
                }
            }
        }
         stage('Deploy') {
            steps {
                echo "Environnement ${params.environment}"
                echo "Version ${params.version}" 
                sshPublisher(publishers: [
                    configName: "serveur_distant",
                    transfers: [
                        sshTransfer(
                            sourceFiles: 'target/*.jar',
                            remoteDirectory: '/tmp'
                        )
                    ]
                ]) 
            }
        }
    }
}
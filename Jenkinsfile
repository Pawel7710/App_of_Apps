def frontendImage="Pawel7710/frontend"
def backendImage="Pawel7710/backend"
def dockerRegistry=""
def registryCredentials="dockerhub"
def backendDockerTag=""
def frontendDockerTag=""


pipeline {

    agent {
        label 'agent'
    }
    
    environment {
    PIP_BREAK_SYSTEM_PACKAGES = 1
    }

    parameters {
    string 'backendDockerTag'
    string 'frontendDockerTag'
    }
    stages {
        stage('Get Code') {
            steps {
                checkout scm
            }
        }
        stage('Adjust version') {
            steps {
                script{
                    backendDockerTag = params.backendDockerTag.isEmpty() ? "latest" : params.backendDockerTag
                    frontendDockerTag = params.frontendDockerTag.isEmpty() ? "latest" : params.frontendDockerTag
                    currentBuild.description = "Backend: ${backendDockerTag}, Frontend: ${frontendDockerTag}"
                }
            }
        }
        stage('Usuniecie uruchomionych kontentenerów') {
        steps {
            sh "docker rm -f frontend backend"
            }
        }
        stage('Deploy application') {
            steps {
                script {
                    withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                             "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                       docker.withRegistry("$dockerRegistry", "$registryCredentials") {
                            sh "docker-compose up -d"
                        }
                    }
                }
            }
        }
        }
        
        stage('Selenium tests') {
            steps {
                sh "pip3 install -r test/selenium/requirements.txt"
                sh "python3 -m pytest test/selenium/frontendTest.py"
            }
        }


        post {
          always {
            sh "docker-compose down"
          cleanWs()
         }
        }
    
}
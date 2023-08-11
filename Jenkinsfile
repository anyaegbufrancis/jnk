def buildNumber = env.BUILD_NUMBER as int
if (buildNumber > 1) milestone(buildNumber - 1)
milestone(buildNumber)

def micrositeName = ''
def git_repo = 'https://github.com/anyaegbufrancis/DO288-apps.git'
def git_branch = 'main'
def appName = 'test'
def appMajorVersion = '0.1.0'
def namespace = 'test'

pipeline {
    agent any
    stages {
        stage('Checkout') {
            when {changeset "hello-world-nginx/*"}
            steps {
                git url: "${git_repo}", branch: "${git_branch}", credentialsId: 'repo-pw'
            }
        }
        stage('Example Deploy') {
            when {
                branch 'production'
                environment name: 'DEPLOY_TO', value: 'production'
            }
            steps {
                echo 'Deploying'
            }
        }
    }
}

//        stage ('Checkout'){
//         when {changeset "hello-world-nginx/*"}
//         steps {
//             git url: "${git_repo}", branch: "${git_branch}", credentialsId: 'repo-pw'
//         }

//    }
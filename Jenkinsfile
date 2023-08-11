def buildNumber = env.BUILD_NUMBER as int
if (buildNumber > 1) milestone(buildNumber - 1)
milestone(buildNumber)

def micrositeName = ''
def git_repo = 'https://github.com/anyaegbufrancis/DO288-apps.git'
def git_branch = 'master'
def appName = 'test'
def appMajorVersion = '0.1.0'
def namespace = 'test'


node ('master') {
   stage ('Checkout'){
      git url: "${git_repo}", branch: "${git_branch}", credentialsId: 'gitlab'

   }
    stage ('Install Dependencies'){
        sh """
        yarn cache clean --force
        yarn install
      """
    }

    stage ('Build Package'){
      sh 'yarn run build'
    }

    stage ('Docker Build'){
        sh """
        docker build -t exampleDockerHubUser/${appName}-nginx-v0:${appMajorVersion} .
      """
    }
    stage ('Docker Push DockerHUB'){
    //     sh """
    //     docker push exampleDockerHubUser/${appName}-nginx-v0:${appMajorVersion}
    //   """
           sh """
                echo "Building..."
              """
    }

    stage ('Deploy to Kubernetes'){
      sh '/root/bin/kubectl get namespace'

    //   sh """
    //     kubectl apply -f ./cicd-template/deployment.yaml --namespace=${namespace} --validate=false
    //     kubectl apply -f ./cicd-template/ingress.yaml --namespace=${namespace}
    //   """
    }

}
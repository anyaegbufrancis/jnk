
def projectName = 'nginx-demo'
def appName = 'nginx01'
def gitUrl = 'https://github.com/anyaegbufrancis/jnk.git'
def gitFolder = 'dev'
def gitBranch = 'master'
def appDomain = 'rhdemolab.com'
def appEndpoint = 'cars'

pipeline {
    agent any
    stages {
        stage('preamble') {
            steps {
                script {
                    openshift.withCluster() {
                          openshift.withProject('default') {
                            def project = openshift.selector("project/${projectName}")
                            if (project.exists()) {
                                echo "${projectName} exists"
                            } else {
                                echo("Creating project: ${projectName}")
                                openshift.newProject("${projectName}")
                                echo "Project: ${projectName} created!"
                            }
                        }
                    }
                }
            }  
        }
        stage('deploy') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject("${projectName}") {
                            def dc = openshift.selector("buildconfigs/${appName}")
                            if (dc.exists()) {
                                echo "Buildconfig: buildconfig/${appName} already exists!"
                                openshift.selector("bc", "${appName}").startBuild("--wait")
                            } else {
                                echo "Deploying App: ${appName}"
                                def created = openshift.newApp("${gitUrl}#${gitBranch}", "--strategy=docker", "--name=${appName}", "--context-dir=${gitFolder}")
                                def builds = created.related('builds')
                                builds.untilEach(1) {
                                    return it.object().status.phase == "Complete"
                                }                            
                            }
                        }
                    }
                }
            }
        }
        stage('route') {
            steps {
                script { 
                    openshift.withCluster() {
                        openshift.withProject("${projectName}") {
                            def route = openshift.selector("route/${appName}")
                            if (route.exists()) {
                                openshift.selector("route", "${appName}").delete()
                                openshift.raw("create", "route", "edge", "${appName}", "--service=${appName}", "--hostname ${appEndpoint}.${appDomain}",  "--insecure-policy=Redirect")
                            } else {
                                openshift.raw("create", "route", "edge", "${appName}", "--service=${appName}", "--hostname ${appEndpoint}.${appDomain}",  "--insecure-policy=Redirect")                                        
                            }
                        }
                    }
                } 
            }
        }
    }
}
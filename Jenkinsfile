def projectName = 'demo-nginx'
def appName = 'nginx01'
def gitUrl = 'https://github.com/anyaegbufrancis/DO288-apps.git'
def gitFolder = 'hello-world-nginx'
def gitBranch = 'master'
def appDomain = 'rhdemolab.com'

pipeline {
            agent {
              node {
                label 'nodejs'
              }
            }
            options {
                timeout(time: 2, unit: 'MINUTES')
            }

            stages {
                stage('preamble') {
                    steps {
                        script {
                            openshift.withCluster() {
                                if !openshift.withProject("${projectName}") {
                                    echo("Creating project: ${projectName}")
                                    openshift.newProject("${projectName}")
                                    echo "Project: ${projectName} created!"
                                } else {
                                    openshift.withProject("${projectName}") {
                                    echo "Using project: ${openshift.project(projectName)}"
                                    }
                                }
                            }
                        }
                    }
                }

                stage('cleanup') {
                    steps {
                        script {
                            openshift.withCluster() {
                                openshift.withProject(projectName) {
                                    openshift.selector("bc", ${appName}).startBuild("--wait")
                                }
                            }
                        } 
                    } 
                } 

                stage('deploy') {
                    when {
                        expression {
                            openshift.withCluster () {
                                return !openshift.selector("dc", ${appName}).exists()
                            }
                        }
                    }
                    steps {
                        script {
                            openshift.withCluster() {
                                openshift.withProject() {
                                    echo "Deploying App: ${appName}"
                                    def created = openshift.newApp("${gitUrl}#${gitBranch}", "--strategy=dev/Dockerfile", "--name=${gitUrl}", "--context-dir=${gitFolder}")
                                    def builds = created.related('builds')
                                    builds.untilEach(1) {
                                        return it.object().status.phase == "Complete"
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
                                    if (openshift.selector("route", "${appName}").exists()) {
                                        openshift.selector("route", "${appName}").delete()
                                        openshift.raw("create", "route", "edge", "--service=${appName}", "--hostname ${appName}.${appDomain}", "--name=${appName}", "--insecure-policy=Redirect")
                                    } else {
                                        openshift.raw("create", "route", "edge", "--service=${appName}", "--hostname ${appName}.${appDomain}", "--name=${appName}", "--insecure-policy=Redirect")                                        
                                    }
                                }
                                }
                                } 
                        }
                    }
                }
        } 
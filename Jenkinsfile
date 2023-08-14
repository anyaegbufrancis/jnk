
def projectName = 'nginx-demo'
def appName = 'nginx01'
def gitUrl = 'https://github.com/anyaegbufrancis/jnk.git'
def gitFolder = 'dev'
def gitBranch = 'master'
def appDomain = 'rhdemolab.com'

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
                            if (openshift.selector("dc", "${appName}").exists()) {
                                echo "Deploymentconfig: ${openshift.deploymentconfig(appName)} already exists!"
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
        // stage('route') {
        //     steps {
        //         script { 
        //             openshift.withCluster() {
        //                 openshift.withProject() {
        //                     if (openshift.selector("route", "${appName}").exists()) {
        //                         openshift.selector("route", "${appName}").delete()
        //                         openshift.raw("create", "route", "edge", "--service=${appName}", "--hostname ${appName}.${appDomain}", "--name=${appName}", "--insecure-policy=Redirect")
        //                     } else {
        //                         openshift.raw("create", "route", "edge", "--service=${appName}", "--hostname ${appName}.${appDomain}", "--name=${appName}", "--insecure-policy=Redirect")                                        
        //                     }
        //                 }
        //             }
        //         } 
        //     }
        // }
    }
}
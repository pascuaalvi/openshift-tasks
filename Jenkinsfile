node('maven') {
  // define commands
  def mvnCmd = "mvn"
  // injection of environment variables is not done so set them here...
  def sourceRef = "master"
  def sourceUrl = "https://github.com/jboss-openshift/openshift-quickstarts.git"
  def devProject = "ocp-tasks"
  def applicationName = "jkf-tasks"

  stage 'extract'
    echo "Extract."
    git branch: sourceRef, url: sourceUrl
    sh "ls -ltr"
    sh "cd tomcat-websocket-chat"
    sh "${mvnCmd} clean install -DskipTests=true"
  stage 'deployInDev'    
    sh "rm -rf oc-build && mkdir -p oc-build/deployments"
    sh "cp target/openshift-tasks.war oc-build/deployments/ROOT.war"
    // clean up. keep the image stream
    sh "oc project ${devProject}"
    sh "oc delete bc,dc,svc,route -l application=${applicationName} -n ${devProject}"
    // create build. override the exit code since it complains about existing imagestream
    sh "oc new-build --name=${applicationName} --image-stream=jboss-webserver30-tomcat8-openshift --binary=true --labels=application=${applicationName} -n ${devProject} || true"
    // build image
    sh "oc start-build ${applicationName} --from-dir=oc-build --wait=true -n ${devProject}"
    // deploy image
    sh "oc new-app ${applicationName}:latest -n ${devProject}"
    sh "oc expose svc/${applicationName} -n ${devProject}"
}

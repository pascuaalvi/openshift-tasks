node('maven') {
  // define commands
  def mvnCmd = "mvn"
  // injection of environment variables is not done so set them here...
  def sourceRef = "master"
  def sourceUrl = "https://github.com/pascuaalvi/openshift-tasks"
  def devProject = "ocp-tasks"
  def applicationName = "jkf-tasks"
  // Jenkins war file
  def WAR_FILE_URL = "https://jenkins-ocp-tasks.b9ad.pro-us-east-1.openshiftapps.com/job/bin-tasks/org.jboss.quickstarts.eap\$jboss-tasks-rs/3/artifact/org.jboss.quickstarts.eap/jboss-tasks-rs/6.4.0-SNAPSHOT/jboss-tasks-rs-6.4.0-SNAPSHOT.war";
  def WAR_FILE_USER = "james.r.duncan@nz.pwc.com";
  def WAR_FILE_PASSWORD = "ocpwc2017";

  stage 'extract'
    echo "Extract."
    echo "Executing curl -u ${WAR_FILE_USER}:${WAR_FILE_PASSWORD} -o $CATALINA_HOME/webapps/ROOT.war -O ${WAR_FILE_URL}"
    sh "curl -u ${WAR_FILE_USER}:${WAR_FILE_PASSWORD} -o $CATALINA_HOME/webapps/ROOT.war -O ${WAR_FILE_URL}"
    //git branch: sourceRef, url: sourceUrl
    //sh "${mvnCmd} clean install -DskipTests=true"
  stage 'test'
    echo "Testing."
    //sh "${mvnCmd} test"
    //echo "Testing Done."
  stage 'deployInDev'
    sh "ls -ltr"       
    // sh "rm -rf oc-build && mkdir -p oc-build/deployments"
    // sh "cp target/openshift-tasks.war oc-build/deployments/ROOT.war"
    // // clean up. keep the image stream
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

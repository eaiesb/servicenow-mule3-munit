#!/usr/bin/groovy
pipeline {
    environment {
        JAVA_HOME = tool('java')
        GU = "${env.GIT_URL}"
    }
agent any
options {
disableConcurrentBuilds()
}
stages {
  
stage("buildsrc") {
steps {
          slackSend (color: "#f1502f", message: "Git URL is : ${env.GIT_URL}")
          slackSend (color: "#3e2c00", message: "GIT_AUTHOR_NAME is : ${env.GIT_AUTHOR_NAME}")
          slackSend (color: "#3e2c00", message: "GIT_COMMIT is : ${env.GIT_COMMIT}")
          slackSend (color: "add8e6", message: 'servicenow-munit-mule3 Deployment Started')
          buildsrc() 
          slackSend (color: "0000ff", message: 'servicenow-munit-mule3 Build Sucessfully')

      }
}

stage('upload to atifactory') {
    steps {
        script{
     def server = Artifactory.server 'artifactory'
     def uploadSpec = """{
  "files": [
    {
      "pattern": "**/*.jar",
      "target": "generic-local/mule3-demo-jars_$BUILD_NUMBER/servicenow-munit-mule3.jar"
    }
 ]
}"""                 
              def buildInfo1 = server.upload spec: uploadSpec
                      slackSend (color: "#FFA500",message: 'servicenow-munit-mule3 Artifacts Uploaded Sucessfully')
            }
    }
}  	
}
   post {
      failure {
            emailext attachLog: true, body: 'Deployment has failed', subject: 'servicenow-munit-mule3 Deployment Status', to: 'sudheekarreddy.donapati@eaiesb.com'
           slackSend (color: "#FF0000",message: 'servicenow-munit-mule3 Deployment Failed')
        }
      success {
          emailext attachLog: true, mimeType: 'text/html', body: '''The following build details are as follows:<br> <br>
<table border="1">
<tr><td style="background-color:#33339F;color:white">,<b>Job Name</b></td><td>$JOB_NAME</td></tr>
<tr><td style="background-color:#33339F;color:white"><b>Build Number</b></td><td>$BUILD_NUMBER</td></tr>
<tr><td style="background-color:#33339F;color:white"><b>Executor Number</b></td><td>$EXECUTOR_NUMBER</td></tr>
<tr><td style="background-color:#33339F;color:white"><b>Build URL</b></td><td>$BUILD_URL</td></tr>
</table>
''', subject: 'Jenkins ${BUILD_STATUS} [#${BUILD_NUMBER}] - ${PROJECT_NAME} ${ENV, var="GIT_URL"}', to: 'sudheekarreddy.donapati@eaiesb.com'    
          slackSend (color: "#32CD32", message: 'servicenow-munit-mule3 Deployment is Sucessful')
        }
  }
}
// steps
def buildsrc() {
dir ('.' ) {
    sh '/usr/maven/apache-maven-3.3.9/bin/mvn package mule:deploy'
}
}

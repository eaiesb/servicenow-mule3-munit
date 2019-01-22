#!/usr/bin/groovy
pipeline {
    environment {
        JAVA_HOME = tool('java')
    }
agent any
options {
disableConcurrentBuilds()
}
stages {
  
stage("Build Mule Source Code") {
steps {
          slackSend (color: "#f1502f", message: "Git URL is : ${env.GIT_URL}")
          slackSend (color: "add8e6", message: 'Servicenow-Munit-Mule3 Deployment Started')
          buildsrc() 
      }
}

stage('Upload Files To Artifactory') {
    steps {
        script{
        sh "echo ${env.GIT_URL} > /tmp/giturl.txt"
     def server = Artifactory.server 'artifactory'
     def uploadSpec = """{
  "files": [
    {
      "pattern": "**/*.zip",
      "target": "generic-local/Servicenow-Munit-Mule3/Servicenow-Munit-Mule3.zip"
    }
 ]
}"""                 
              def buildInfo1 = server.upload spec: uploadSpec

            }
    }
}  	
}
   post {
      failure {
                slackSend (color: "0000ff", message: 'Servicenow-Munit-Mule3 Build failed')
            emailext attachLog: true, body: '''The Failed build details are as follows:<br> <br>
<table border="1">
<tr><td style="background-color:white;color:red"><b>Job Name</b></td><td>$JOB_NAME</td></tr>
<tr><td style="background-color:white;color:red"><b>Build Number</b></td><td>$BUILD_NUMBER</td></tr>
<tr><td style="background-color:white;color:red"><b>GIT URL</b></td><td>${FILE, path="/tmp/giturl.txt"}</td></tr>
<tr><td style="background-color:white;color:red"><b>Build URL</b></td><td>$BUILD_URL</td></tr>
</table>
''', subject: 'Servicenow-Munit-Mule3 Deployment Status', to: 'sudheekarreddy.donapati@eaiesb.com'
           slackSend (color: "#FF0001",message: 'Servicenow-Munit-Mule3 Deployment Failed')
        }
      success {
          slackSend (color: "0000ff", message: 'Servicenow-Munit-Mule3 Build sucess')
          slackSend (color: "#FFA500",message: 'Servicenow-Munit-Mule3 Artifacts Uploaded Sucessfully')
          emailext attachLog: true, mimeType: 'text/html', body: '''The jenkins build details are as follows:<br> <br>
<table border="1">
<tr><td style="background-color:#33339F;color:white"><b>Job Name</b></td><td>$JOB_NAME</td></tr>
<tr><td style="background-color:#33339F;color:white"><b>Build Number</b></td><td>$BUILD_NUMBER</td></tr>
<tr><td style="background-color:#33339F;color:white"><b>GIT URL</b></td><td>${FILE, path="/tmp/giturl.txt"}</td></tr>
<tr><td style="background-color:#33339F;color:white"><b>Build URL</b></td><td>$BUILD_URL</td></tr>
</table>
''', subject: 'Jenkins ${BUILD_STATUS} [#${BUILD_NUMBER}] - ${PROJECT_NAME} ${ENV, var="GIT_URL"}', to: 'sudheekarreddy.donapati@eaiesb.com'    
          slackSend (color: "#32CD32", message: 'Servicenow-Munit-Mule3 Deployment is Sucessful')
        }
  }
}
// steps
def buildsrc() {
dir ('.' ) {
     sh '/usr/maven/apache-maven-3.3.9/bin/mvn clean package mule:deploy'
}
}
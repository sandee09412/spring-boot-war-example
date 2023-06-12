pipeline {
    agent any
     tools {
        maven 'MAVEN_HOME' 
        }
    stages {
        stage("git checkout"){
            steps{
                git url: 'https://github.com/sandee09412/spring-boot-war-example.git'
            }
            }
        stage("Test"){
            steps{
                // mvn test
               // sh "mvn test"
                  bat "mvn test"
               // slackSend channel: 'youtubejenkins', message: 'Job Started'
               // fileExists 'C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\Maven-project-pipeline\\target\\hello-world-1.$(BUILD_NUMBER)-SNAPSHOT.war'
            }
            
        }
        
        stage("Build"){
            steps{
                  bat "mvn package"
                //sh "mvn package"
               // slackSend channel: 'youtubejenkins', message: 'Job build'
               //   bat 'echo $WORKSPACE/Artifact_$BUILD_NUMBER'
            }
            
        }
        stage("Artifect file exist"){
            steps{
               echo 'cheking file in work space'
                fileExists 'C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\Maven-project-pipeline\\target\\hello-world-1.$(BUILD_NUMBER)-SNAPSHOT.war'
                echo 'fileExists C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\Maven-project-pipeline\\target\\hello-world-1.$(BUILD_NUMBER)-SNAPSHOT.war'
            }
        }
         stage ('Server'){
            steps {
               rtServer (
                 id: "Artifactory",
                 url: 'http://43.205.115.185:8082/artifactory',
                 username: 'sandeep',
                  password: 'Troy@567',
                  bypassProxy: true,
                   timeout: 300
                        )
            }
        }
        stage('Upload'){
            steps{
                rtUpload (
                  //  buildNumber: BUILD_NUMBER,
                  //  buildName: JOB_NAME,
          
                 serverId:"Artifactory" ,
                  spec: '''{
                   "files": [
                      {
                      "pattern": "*.war",
                      "target": "logic-ops-lab-libs-snapshot-local",
                      "recursive": "false"
                      }
                            ]
                           }''',
                        )
            }
        }
        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "Artifactory"
                )
            }
        }
//         stage ('Upload') {
//             steps {
//                 rtUpload (
//                     buildName: JOB_NAME,
//                     buildNumber: BUILD_NUMBER,
//                     serverId: SERVER_ID, // Obtain an Artifactory server instance, defined in Jenkins --> Manage:
//                     spec: '''{
//                               "files": [
//                                  {
//                                   "pattern": "$WORKSPACE/Demo-Artifactory/Artifact_*",
//                                   "target": "result/",
//                                   "recursive": "false"
//                                 } 
//                              ]
//                         }'''    
//                     )
//             }
//         }
//         stage ('Publish build info') {
//             steps {
//                 rtPublishBuildInfo (
//                     buildName: JOB_NAME,
//                     buildNumber: BUILD_NUMBER,
//                     serverId: SERVER_ID
//                 )

//                 rtPublishBuildInfo (
//                     buildName: JOB_NAME,
//                     buildNumber: BUILD_NUMBER,
//                     serverId: SERVER_ID
//                 )
//             }
//         }
        stage("Deploy on Test"){
            steps{
                // deploy on container -> plugin
                  deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://13.235.17.161:8080')], contextPath: '/app', war: '**/*.war'
            }
            
        }
        stage("Deploy on Prod"){
               input {
                   message "Should we continue?"
                   ok "Yes we Should"
               }
            
               steps{
                   // deploy on container -> plugin 13.127.93.35
                   deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://13.235.42.137:8080')], contextPath: '/app', war: '**/*.war'

               }
        }
       
    }
    post{
      //  success {
    // emailext attachLog: true, body: 'Email sent out from Jenkins', subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: 'prabhakarq3@gmail.com'
    // }
  // failure {
   //  emailext attachLog: true, body: 'Email sent out from Jenkins', subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: 'prabhakarq3@gmail.com'
  // }
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
          //  slackSend channel: 'youtubejenkins', message: 'Job Success'
        }
        failure{
            echo "========pipeline execution failed========"
           // slackSend channel: 'youtubejenkins', message: 'Job UnSuccess'
        } 
    }
   
}

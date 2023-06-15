pipeline {
    agent any
     tools {
        maven 'MAVEN_HOME' 
        }
    // stages {
    //     stage("git checkout"){
    //         steps{
    //            // git url: 'https://github.com/sandee09412/spring-boot-war-example.git'
    //             git branch: 'master', url: 'https://github.com/sandee09412/spring-boot-war-example.git'
    //         }
    //         }
        stage("Test"){
            steps{
                  bat "mvn test"
               // slackSend channel: 'youtubejenkins', message: 'Job Started'
                
            }
            
        }
        stage("Build"){
            steps{
                  bat "mvn package"
                //sh "mvn package"
            }
            
        }
        stage ('Server'){
            steps {
               rtServer (
                 id: "Artifactory",
                 url: 'http://13.235.243.28:8082/artifactory',
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
                 buildNumber: BUILD_NUMBER,
                 buildName: JOB_NAME,
          
                 serverId:"Artifactory" ,
                  spec: '''{
                   "files": [
                      {
                      "pattern": "*.war",
                      "target": "logic-ops-lab-libs-snapshot-local"
                      }
                            ]
                           }''',
                        )
            }
        }
//         stage('Build and Upload Artifact') {
//     steps {
//         script {
//             def server = Artifactory.server('Artifactory')
//             server.upload(
//                 fileSpec: [
//                   //  pattern: 'target/*.jar',
//                     pattern: '*.war',
//                      target: "${logic-ops-lab-libs-snapshot-local}/${ARTIFACT_VERSION}/"
//                    // target: "${REPOSITORY_NAME}/${ARTIFACT_VERSION}/"
//                 ]
//             )
//         }
//     }
// }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "Artifactory"
                )
            }
        }
        stage("Deploy on Test"){
            steps{
                // deploy on container -> plugin
                  deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://52.66.172.199:8080')], contextPath: '/app', war: '**/*.war'
              
            }
            
        }
        stage("Deploy on Prod"){
               input {
                   message "Should we continue?"
                   ok "Yes we Should"
               }
            
               steps{
                   // deploy on container -> plugin 13.127.93.35
                   deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://65.1.84.56:8080')], contextPath: '/app', war: '**/*.war'
               }
        }
        // stage ('Removing files') {
        //     steps {
        //        // bat 'rm -rf *.war'
        //         bat 'rmdir *.war'
        //         //sh 'rm -rf $C:/ProgramData/Jenkins/.jenkins\workspace\Maven-project-pipeline\target\hello-world-0.0.1-SNAPSHOT.war'
               
        //     }
        // }
       
    }
    post{
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

pipeline {
    agent any
    
     tools {
        maven 'MAVEN_HOME' 
        }
    environment {
        FILE_PATH = 'C:/ProgramData/Jenkins/.jenkins/workspace/Maven-project-pipeline/target/'
        REPOSITORY_NAME = 'result'  
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
        stage('Upload Artifact') {
            steps {
                script {
                    def artifactVersion = bat(script: 'C:\Program Files\Maven\apache-maven-3.8.2 help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()
                   // def artifactVersion = sh(script: '/path/to/maven/bin/mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()

                    rtMavenDeployer(
                        id: 'Artifactory',
                        releaseRepo: "${REPOSITORY_NAME}/${artifactVersion}",
                        snapshotRepo: "${REPOSITORY_NAME}/snapshot",
                        server: 'Artifactory'
                    ) {
                        deployArtifacts()
                    }
                }
            }
        }
//          stage ('Server'){
//             steps {
//                rtServer (
//                  id: "Artifactory",
//                  url: 'http://43.205.214.12:8082/artifactory',
//                  username: 'sandeep',
//                   password: 'Troy@567',
//                   bypassProxy: true,
//                    timeout: 300
//                         )
//             }
//         }
//         stage('Build and Upload Artifact') {
//     steps {
//         script {
//            // bat 'mvn clean package'
//            // def server = Artifactory.server('Artifactory')
//             server.upload(
//                 fileSpec: [
//                     pattern: '*.war',
//                     target: "${result}/${ARTIFACT_VERSION}/"
//                 ]
//             )
//         }
//     }
// }

        // stage('Upload'){
        //     steps{
        //         rtUpload (
        //          // buildNumber: BUILD_NUMBER,
        //          // buildName: JOB_NAME,
          
        //          serverId:"Artifactory" ,
        //           spec: '''{
        //            "files": [
        //               {
        //               "pattern": "*.war",
        //               "target": "example-repo-local"
        //               }
        //                     ]
        //                    }''',
        //                 )
        //     }
        // }
        // stage ('Publish build info') {
        //     steps {
        //         rtPublishBuildInfo (
        //             serverId: "Artifactory"
        //         )
        //     }
        // }
        stage("Deploy on Test"){
            steps{
                // deploy on container -> plugin
                  deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://52.66.19.85:8080')], contextPath: '/app', war: '**/*.war'
                //deploy adapters: [tomcat9(credentialsId: 'tomcatnew', path: '', url: 'http://3.111.168.163:8080')], contextPath: 'app', war: '**/*.war'
                //deploy adapters: [tomcat9(credentialsId: 'tomcatserver', path: '', url: 'http://13.233.106.97:8080')], contextPath: '/apps', war: '**/*.war'
                //deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://13.233.106.97:8080')], contextPath: '/app', war: '**/*.war'
              
            }
            
        }
        stage("Deploy on Prod"){
               input {
                   message "Should we continue?"
                   ok "Yes we Should"
               }
            
               steps{
                   // deploy on container -> plugin 13.127.93.35
                   deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://52.66.203.244:8080')], contextPath: '/app', war: '**/*.war'
//                  //  deploy adapters: [tomcat9(credentialsId: 'tomcatserver', path: '', url: 'http://13.232.70.143:8080')], contextPath: '/app', war: '**/*.war'
                   //deploy adapters: [tomcat9(credentialsId: 'tomcatnew', path: '', url: 'http://13.233.245.75:8080')], contextPath: 'app', war: '**/*.war'
//                 //deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://3.108.193.74:8080')], contextPath: '/app', war: '**/*.war'

               }
        }
       
    }
    post{
   //      success {
   //   emailext attachLog: true, body: 'Email sent out from Jenkins', subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: 'prabhakarq3@gmail.com'
   //   }
   // failure {
   //   emailext attachLog: true, body: 'Email sent out from Jenkins', subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: 'prabhakarq3@gmail.com'
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

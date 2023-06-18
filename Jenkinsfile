pipeline {
    agent any
    
     tools {
        maven 'MAVEN_HOME' 
        }
    environment {
        FILE_PATH = 'local/C:/ProgramData/Jenkins/.jenkins/workspace/Maven-project-pipeline/target/*.war'
        REPOSITORY_NAME = 'logic-ops-lab-libs-snapshot-local'  
    }
    stages {
        stage("git checkout"){
            steps{
                git url: 'https://github.com/sandee09412/spring-boot-war-example.git'
            }
            }
        stage("Test"){
            steps{
                  bat "mvn test"
               // slackSend channel: 'youtubejenkins', message: 'Job Started'
                
            }
            
        }
        stage("Build"){
            steps{
                  bat "mvn package"
            }
            
        }
        stage ('Server'){
            steps {
               rtServer (
                 id: "Artifactory",
                 url: 'http://13.127.107.133:8082/artifactory',
                 username: 'sandeep',
                  password: 'Troy@567',
                  bypassProxy: true,
                   timeout: 300
                        )
            }
        }
        stage('Upload Artifact') {
            steps {
                script {
                    def artifactVersion = bat(
                        script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout',
                        returnStdout: true
                    ).trim()

                    def filePath = bat(
                        script: 'echo $FILE_PATH',
                        returnStdout: true
                    ).trim()

                    def server = Artifactory.newServer url: 'http://13.127.107.133:8082/artifactory', credentialsId: 'Artifactory'
                    def uploadSpec = """
                    {
                        "files": [
                            {
                                "pattern": "local/${filePath.replaceAll('\\\\', '\\\\\\\\')}/*.war",
                                "target": "${REPOSITORY_NAME}/${artifactVersion.replaceAll('[\r\n]+', '')}/"
                            }
                        ]
                    }
                    """

                    server.upload(uploadSpec)
                }
            }
        }
    // stage('Upload Artifact') {
    //         steps {
    //             script {
    //                 def artifactVersion = bat(
    //                     script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout',
    //                     returnStdout: true
    //                 ).trim()

    //                 def server = Artifactory.newServer url: 'http://13.127.107.133:8082/artifactory', credentialsId: 'Artifactory'
    //                 def uploadSpec = """{
    //                     "files": [
    //                         {
    //                             "pattern": "${FILE_PATH}",
    //                             "target": "${REPOSITORY_NAME}/${artifactVersion}/"
    //                         }
    //                     ]
    //                 }"""

    //                 server.upload(uploadSpec)
    //             }
    //         }
    //     }
        // stage('Upload Artifact') {
        //     steps {
        //         script {
        //             def artifactVersion = bat(script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()
        //            // def artifactVersion = sh(script: '/path/to/maven/bin/mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()

        //             rtMavenDeployer(
        //                 id: "Artifactory",
        //                 releaseRepo: "${REPOSITORY_NAME}/${artifactVersion}",
        //                 snapshotRepo: "${REPOSITORY_NAME}/snapshot",
        //                 server: "Artifactory"
        //             ) {
        //                 deployArtifacts()
        //             }
        //         }
        //     }
        // }
         
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

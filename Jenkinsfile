pipeline {
    agent any
    
     tools {
        maven 'MAVEN_HOME' 
        }
    environment {
        REPOSITORY_NAME = 'logic-ops-lab-libs-snapshot-local' 
    }
    stages {
        stage("git checkout"){
            steps{
                git url: 'https://github.com/sandee09412/spring-boot-war-example.git'
                slackSend channel: 'youtubejenkins', message: 'Code checkout successfully....---------------====='
            }
            }
        stage("Test"){
            steps{
                  bat "mvn test"
                  slackSend channel: 'youtubejenkins', message: 'Test cases run successfully '
                
            }
            
        }
        stage("Build"){
            steps{
                  bat "mvn package"
                  slackSend channel: 'youtubejenkins', message: 'job build successfully'
            } 
        }
//          stage('SonarQube analysis') {
// //    def scannerHome = tool 'SonarScanner 4.0';
//         steps{
//         withSonarQubeEnv('sonarqube-8.9') { 
//         // If you have configured more than one global server connection, you can specify its name
// //      sh "${scannerHome}/bin/sonar-scanner"
//         sh "mvn sonar:sonar"
//     }
//         }
//         }
        stage ('Server'){
            steps {
               rtServer (
                 id: "Artifactory",
                 url: 'http://ec2-43-205-228-6.ap-south-1.compute.amazonaws.com:8082/artifactory',
                 username: 'sandeep',
                  password: 'Troy@567',
                  bypassProxy: true,
                   timeout: 300
                        )
                slackSend channel: 'youtubejenkins', message: 'validating Jfrog server details'
            }
        }

        stage('Upload'){
            steps{
                script {
                    def jobNumber = currentBuild.number
                rtUpload (
                 serverId:"Artifactory" ,
                  spec: """{
                   "files": [
                      {
                      "pattern": "*.war",
                      "target": "${REPOSITORY_NAME}/${jobNumber}/"
                      }
                            ]
                           }""",
                         )
                     }  
                slackSend channel: 'youtubejenkins', message: 'Artifect uploded successfully on Jfrog'
            }
        }
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
                  deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://ec2-13-235-245-26.ap-south-1.compute.amazonaws.com:8080')], contextPath: '/app', war: '**/*.war'
                //deploy adapters: [tomcat9(credentialsId: 'tomcatnew', path: '', url: 'http://3.111.168.163:8080')], contextPath: 'app', war: '**/*.war'
                  slackSend channel: 'youtubejenkins', message: 'Artifact successfully deployed to Test server'
            }
            
        }
        stage("Deploy on Prod"){
               input {
                   message "Should we continue?"
                   ok "Yes we Should"
               }
            
               steps{
                   // deploy on container -> plugin 13.127.93.35
                   deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://ec2-15-207-106-135.ap-south-1.compute.amazonaws.com:8080')], contextPath: '/app', war: '**/*.war'
                   //  deploy adapters: [tomcat9(credentialsId: 'tomcatserver', path: '', url: 'http://13.232.70.143:8080')], contextPath: '/app', war: '**/*.war'
                   slackSend channel: 'youtubejenkins', message: 'Artifact successfully deployed to production server'
               }
        }
       
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
            slackSend channel: 'youtubejenkins', message: 'Job Success'
        }
        failure{
            echo "========pipeline execution failed========"
            slackSend channel: 'youtubejenkins', message: 'Job UnSuccess'
        } 
    }
   
}

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
                slackSend channel: 'youtubejenkins', message: 'checkout code from repo'
            }
            }
        stage("Test"){
            steps{
                  bat "mvn test"
                  slackSend channel: 'youtubejenkins', message: 'Maven test job '
                
            }
            
        }
        stage("Build"){
            steps{
                  bat "mvn package"
                  slackSend channel: 'youtubejenkins', message: 'build job success'
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
                 url: 'http://ec2-13-232-141-158.ap-south-1.compute.amazonaws.com:8082/artifactory',
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
                slackSend channel: 'youtubejenkins', message: 'Artifect uploded successful on Jfrog'
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
                  deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://ec2-13-232-18-223.ap-south-1.compute.amazonaws.com:8080')], contextPath: '/app', war: '**/*.war'
                //deploy adapters: [tomcat9(credentialsId: 'tomcatnew', path: '', url: 'http://3.111.168.163:8080')], contextPath: 'app', war: '**/*.war'
                  slackSend channel: 'youtubejenkins', message: 'Artifect deploy  successful on test server'
            }
            
        }
        stage("Deploy on Prod"){
               input {
                   message "Should we continue?"
                   ok "Yes we Should"
               }
            
               steps{
                   // deploy on container -> plugin 13.127.93.35
                   deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://ec2-52-66-209-8.ap-south-1.compute.amazonaws.com:8080')], contextPath: '/app', war: '**/*.war'
                   //  deploy adapters: [tomcat9(credentialsId: 'tomcatserver', path: '', url: 'http://13.232.70.143:8080')], contextPath: '/app', war: '**/*.war'
                   slackSend channel: 'youtubejenkins', message: 'Artifect deploy  successful on prod server'
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

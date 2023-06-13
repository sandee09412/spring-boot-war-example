pipeline {
    agent any
     tools {
        maven 'MAVEN_HOME' 
        }
    stages {
        stage("git checkout"){
            steps{
               // git url: 'https://github.com/sandee09412/spring-boot-war-example.git'
                git branch: 'master', url: 'https://github.com/sandee09412/spring-boot-war-example.git'
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
                //sh "mvn package"
            }
            
        }
        stage ('Server'){
            steps {
               rtServer (
                 id: "Artifactory",
                 url: 'http://3.110.170.191:8082/artifactory',
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
                  deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://13.235.16.158:8080')], contextPath: '/app', war: '**/*.war'
              
            }
            
        }
        stage("Deploy on Prod"){
               input {
                   message "Should we continue?"
                   ok "Yes we Should"
               }
            
               steps{
                   // deploy on container -> plugin 13.127.93.35
                   deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://52.66.212.174:8080')], contextPath: '/app', war: '**/*.war'
               }
        }
        stage ('Removing files') {
            steps {
                sh 'rm -rf $WORKSPACE/*'
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

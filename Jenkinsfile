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
                
            }
            
        }
        stage("Build"){
            steps{
                  bat "mvn package"
                //sh "mvn package"
               // slackSend channel: 'youtubejenkins', message: 'Job build'
            }
            
        }
        stage("Deploy on Test"){
            steps{
                // deploy on container -> plugin
                  deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://43.204.37.160:8080')], contextPath: '/app', war: '**/*.war'
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
                   deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://43.204.19.4:8080')], contextPath: '/app', war: '**/*.war'
//                  //  deploy adapters: [tomcat9(credentialsId: 'tomcatserver', path: '', url: 'http://13.232.70.143:8080')], contextPath: '/app', war: '**/*.war'
                   //deploy adapters: [tomcat9(credentialsId: 'tomcatnew', path: '', url: 'http://13.233.245.75:8080')], contextPath: 'app', war: '**/*.war'
//                 //deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails', path: '', url: 'http://3.108.193.74:8080')], contextPath: '/app', war: '**/*.war'

               }
        }
        stage ('Server'){
            steps {
               rtServer (
                 id: "Artifactory",
                 url: 'http://localhost:8082/artifactory',
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

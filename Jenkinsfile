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
            }
            
        }
        
        stage("Build"){
            steps{
                  bat "mvn package"
            }
            
        }
           stage("Artifect file exist"){
               steps{
                  echo 'cheking file in work space'
                   fileExists 'C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\Maven-project-pipeline\\target\\hello-world-1.$(BUILD_NUMBER)-SNAPSHOT.war'
                   echo 'file exist'
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
                 serverId:"Artifactory" ,
                  spec: '''{
                   "files": [
                      {
                      //"pattern": "*.war",
                      "pattern": "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\Maven-project-pipeline\\target\\hello-world-1.$(BUILD_NUMBER)-SNAPSHOT.war",
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

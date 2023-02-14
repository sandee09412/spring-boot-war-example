pipeline {
    agent any
     tools {
        maven 'Maven' 
        }
    stages {
        stage("Test"){
            steps{
                // mvn test
                sh "mvn test"
               //slackSend channel: 'youtubejenkins', message: 'Job Started'
                
            }
            
        }
        stage("Build"){
            steps{
                sh "mvn package"
                
            }
            
        }
        stage("Deploy on Test"){
            steps{
                // deploy on container -> plugin
                deploy adapters: [tomcat9(credentialsId: 'tomcatnew', path: '', url: 'http://135.154.16.246:8080')], contextPath: 'app', war: '**/*.war'
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
                   // deploy on container -> plugin
//                    deploy adapters: [tomcat9(credentialsId: 'tomcatserver', path: '', url: 'http://13.109.60.246:8080')], contextPath: '/app', war: '**/*.war'
                   deploy adapters: [tomcat9(credentialsId: 'tomcatnew', path: '', url: 'http://3.109.60.246:8080')], contextPath: 'app', war: '**/*.war'
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
             //slackSend channel: 'youtubejenkins', message: 'Success'
        }
        failure{
            echo "========pipeline execution failed========"
            // slackSend channel: 'youtubejenkins', message: 'Job Failed'
        }
    }
}

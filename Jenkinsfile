pipeline {
    agent any
    tools {
        maven "Maven3"
    }
    
    stages {
        stage('Checkout') {
            steps {
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'c5d1add2-cbcb-470b-88f0-81b0076f9d93', url: 'https://github.com/akannan1087/myMar2022WeekdayBatchRepo']]])

            }
        }
        
        stage ("Build") {
            steps {
                sh "mvn clean install -f MyWebApp/pom.xml"
            }
            
        }

        stage ("Code scan") {
            steps {
                withSonarQubeEnv("Sonarqube") {
                sh "mvn sonar:sonar -f MyWebApp/pom.xml"
            }
         }
        }
        
        stage ("Nexus upload") {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: '03405c4b-8bfd-42f6-94b5-2fcb1657b7c0', groupId: 'com.dept.app', nexusUrl: 'ec2-3-140-187-14.us-east-2.compute.amazonaws.com:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'

            }
        }
        
        stage ("DEV deploy") {
            steps {
                 deploy adapters: [tomcat9(credentialsId: '3fc8c614-a0c1-4fae-a0ce-af884cccf5d1', path: '', url: 'http://ec2-3-135-116-26.us-east-2.compute.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'   

            }
        }
        
        stage ("Slack") {
            steps {
                slackSend channel: 'mar-2022-weekday-batch,feb-2022-weekday-batch', message: 'DEV Deployment was successful, please start DEV testing'
            }
        }
    }
}

pipeline {
    agent any
    tools {
        maven "maven3.9.9"
    }

    stages {
        stage ("Git clone") {
            steps {
                git branch: 'main', url: 'https://github.com/Kennethopare465/web-app1.git'
            }
        }

        stage ("Build with Maven") {
            steps {
                sh "mvn clean"
            }
        }

        stage ("Testing with Maven") {
            steps {
                sh "mvn test"
            }
        }

        stage ("Package with Maven") {
            steps {
                sh "mvn package"
            }
        }

        stage ("SonarQube Analysis") {
            environment {
                ScannerHome = tool 'sonar5.0'
            }
            steps {
                script {
                    withSonarQubeEnv('sonarqube') {
                        sh '${ScannerHome}/bin/sonar-scanner -Dsonar.projectKey=kennetho'
                    }
                }
            }
        }
        stage ("Upload to Nexus") {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'maven-web-application', classifier: '', file: '/var/lib/jenkins/workspace/webapp-project/target/web-app.war', type: 'war']], credentialsId: 'nexus-id', groupId: 'com.mt', nexusUrl: '16.171.234.79:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'webapp-release', version: '3.1.2-RELEASE'
            }
        }

        stage ("Deploy to UAT") {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat-id', path: '', url: 'http://16.171.113.165:8080')], contextPath: null, war: 'target/*.war'
            }
        }
    }
}

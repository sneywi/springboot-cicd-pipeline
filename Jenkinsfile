pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout ') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/sneywi/springboot-cicd-pipeline.git'
            }
        }
        
        stage('Code Compile') {
            steps {
                    sh "mvn compile"
            }
        }
        
        stage('Run Test Cases') {
            steps {
                    sh "mvn test"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                    withSonarQubeEnv('sonar-server') {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Java-WebApp \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=Java-WebApp '''
    
                }
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                   dependencyCheck additionalArguments: '--scan ./   ', odcInstallation: 'DP'
                   dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Maven Build') {
            steps {
                    sh "mvn clean compile"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                   script {
                       withDockerRegistry(credentialsId: 'b289dc43-2ede-4bd0-95e8-75ca26100d8d', toolName: 'docker') {
                            sh "docker build -t springboot-webapp ."
                            sh "docker tag springboot-webapp sney2307/springboot-webapp:latest"
                            sh "docker push sney2307/springboot-webapp:latest "
                        }
                   } 
            }
        }
        
        stage('Docker Image scan') {
            steps {
                    sh "trivy image sney2307/springboot-webapp:latest "
            }
        }
        
    }
}

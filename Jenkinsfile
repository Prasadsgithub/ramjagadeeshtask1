pipeline {
    agent { label 'NODE2' }
    options {
        timeout(time: 1, unit: 'HOURS')
    }
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage('source code') {
            steps {
                git url: 'https://github.com/Prasadsgithub/spring-petclinic.git' ,
                branch: 'main'
            }
        }
        stage('Build the code and sonarqube analysis') {
            steps {
                withSonarQubeEnv('SONAR_LATEST') {
                    sh 'mvn clean package sonar:sonar'
                }
            }
        }
        stage('Artifactory-Configuration') {
            steps {
                rtMavenDeployer (
                    id: 'spc-deployer',
                    serverId: 'JFROG_LATEST',
                    releaseRepo: "spring-new-libs-release",
                    snapshotRepo: "spring-new-libs-snapshot"
                    
                )
            }
        }
        
        stage('Build & Deploy the Code') {
            steps {
                rtMavenRun (
                    // Tool name from Jenkins configuration.
                    tool: 'MAVEN_HOME',
                    pom: 'pom.xml',
                    goals: 'install',
                    // Maven options.
                    deployerId: 'spc-deployer'
                )
            }
        }  
        stage('Reporting') {
            steps {
                junit testResults: '**/surefire-reports/*.xml'
            }  
        }
    }
}
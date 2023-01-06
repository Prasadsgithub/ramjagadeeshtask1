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
                    releaseRepo: "spring-new-libs-release-local",
                    snapshotRepo: "spring-new-libs-snapshot-local"
                )
            }
        } 
        stage('Build & Deploy the Code') {
            steps {
                rtMavenRun (
                    // Tool name from Jenkins configuration.
                    tool: 'MAVEN_HOME',
                    pom: 'pom.xml',
                    goals: 'clean install',
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

// def RES = "reddy.jfrog.io/brep-dev/"
// def appname = test
// if (gitBranch.contains('master')) {
//     docker build image -t {RES}-{appname}:{Buildnumber}
//     //reddy.jfrog.io/brep-dev/test:8
// }else if (gitBranch.contains('feature/')) {feature/lambda-test
//        env.extractbranch = gitBranch.sampleprinter("/")[0].toLowerCase()
//        env.extractbranch1 = gitBranch.sampleprinter("/")[1].toLowerCase()
//        docker build image -t {RES}-{appname}:{extractbranch}-{extractbranch1}-{Buildnumber}
//        //reddy.jfrog.io/brep-dev/test:feature-lambda-test-8
// }else {
//     echo "image not build"
// } (edited) 
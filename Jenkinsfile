pipeline {
    agent { label 'nodejs' }
    options { 
        timeout(time: 1, unit: 'HOURS')
        retry(2) 
    }
    triggers {
        cron('0 * * * *')
    }
    parameters {
        choice(name: 'GOAL', choices: ['compile', 'package', 'clean package'])
    }
    stages {
        stage('Source Code') {
            steps {
                git url: 'https://github.com/thiru9880/spring-petclinic-thiru.git',
                branch: 'main'
            }
        }
        stage('artifactory'){
            steps{
                rtMavenDeployer (
                    id: 'spc-deployer',
                    serverId: 'JFROG-LOCAL',
                    releaseRepo: 'new-repo-libs-release-local',
                    snapshotRepo: 'new-repo-libs-snapshot-local',
                    )
                rtMavenRun (
                    tool: 'MVN_PATH',
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: 'spc-deployer',
                    )

            }
        }
        stage('Build the Code and sonarqube-analysis') {
            steps {
                withSonarQubeEnv('sonar-demo') {
                    // sh script: "mvn package sonar:sonar"
                    sh script: "mvn package"
                }

            }
        }
        stage('reporting') {
            steps {
                junit testResults: 'target/surefire-reports/*.xml'
            }
        }

    }

}

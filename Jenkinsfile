pipeline {
    agent { label 'JDK'}
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage('vcs') {
            steps {
                git url: 'https://github.com/lakshminarayanamantigmailcom/spring-petclinic.git',
                    branch: 'main'
            }
        }
        stage('SonarQube analysis') {
            steps {

                // performing sonarqube analysis with "withSonarQubeENV(<Name of Server configured in Jenkins>)"
                withSonarQubeEnv('SONAR_CLOUD') {
                    sh 'mvn  package sonar:sonar -Dsonar.organization=testdevops123 -Dsonar.token=0985970896bb032eba889a894ccacfcc18d8c254 -Dsonar.projectKey=testdevops123_jenkins'
                }
            }
        }
        stage('build and package') {
            steps {
                 rtMavenDeployer (
                    id: "SPC_DEPLOYER",
                    serverId: "JFROG",
                    releaseRepo: 'jenkins-app-libs-snapshot-local',
                    snapshotRepo: 'jenkins-app-libs-snapshot-local'
                )
                rtMavenRun (
                    tool: 'MAVEN_3.9', // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'package',
                    deployerId: "SPC_DEPLOYER"
                    //,
                    //buildName: "${JOB_NAME}",
                    //buildNumber: "${BUILD_ID}"
                )
                rtPublishBuildInfo (
                    serverId: "JFROG"
                )
            }
        }
        stage('reporting') {
            steps {
                junit testResults: '**/target/surefire-reports/TEST-*.xml'
            }
        }
    }

}


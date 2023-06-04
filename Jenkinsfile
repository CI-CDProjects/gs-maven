pipeline {
    agent { label 'Anti-Node' }
    triggers { pollSCM('* * * * *') }
    parameters {
        choice(name: 'MAVEN_GOAL', choices: ['package', 'install', 'clean', 'test', 'compile'], description: 'These are Maven goals.')
    }
    stages {
        stage('VCS') {
            steps {
                git url: 'https://github.com/CI-CDProjects/gs-maven.git',
                    branch: 'decl'
            }
        }
        stage('Build the Package') {
        tools {
            jdk 'JDK_17'
            }
            steps {
                sh "cd complete && mvn ${params.MAVEN_GOAL}"
            }
        }
        stage('Archive the Package') {
            steps {
                archiveArtifacts onlyIfSuccessful: true,
                                artifacts: '**/target/gs-maven-0.1.0.jar',
                                allowEmptyArchive: false
            }
        }
        stage('Publish the Test Results') {
            steps {
                junit testResults: '**/target/surefire-reports/Test-*.xml',
                    allowEmptyResults: true
            }
        }
    }
    post {            
        success {
            mail to: 'tqdevops-team@qt.com',
                from: 'tqdevops@qt.com',
                subject: "Jenkins build status of ${JOB_NAME} with id ${BUILD_ID}",
                body: "Refer Here ${BUILD_URL} for more details. Build is Successful"
        }
        failure {
            mail to: "${GIT_AUTHOR_EMAIL}",
                from: 'tqdevops@qt.com',
                subject: "Jenkins build status of ${JOB_NAME} with id ${BUILD_ID}",
                body: "Refer Here ${BUILD_URL} for more details. Build has Failed"
        }
    }
}

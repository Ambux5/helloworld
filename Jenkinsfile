pipeline {
    agent { label 'gcc' }
    environment {
        CI = true
        AGENT_ACCESS = credentials('credential-for-artifactory')
    }
    stages {
        stage('download') {
            steps {
                sh 'rm -rf helloworld'
                sh 'git clone https://github.com/Ambux5/helloworld.git/'
                echo 'downloaded'
            }
        }
        stage('build') {
            steps {
                sh 'gcc helloworld/main.c -o output'
            }
        }
        stage('package') {
            steps {
                sh 'zip -r helloworld-0.0.${BUILD_NUMBER} output'
            }
        }
        stage('push to artifactory') {
            steps {
                sh 'curl -sSf -u ${AGENT_ACCESS} -X PUT -T helloworld-0.0.${BUILD_NUMBER} "http://10.5.0.8:8081/artifactory/example-repo-local/helloworld-0.0.${BUILD_NUMBER}.zip"'
                sh 'rm -r *'
            }
        }
    }
}

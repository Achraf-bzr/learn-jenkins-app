pipeline {
    agent any

    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }   
                echo 'Hello World'
            }
            stage('Build') {
             steps {
                sh 'echo ls -a'
                sh 'npm --version'
                sh 'node --version'
                sh 'npm ci'
                sh 'npm run build'
    }
}
        }
    }
}

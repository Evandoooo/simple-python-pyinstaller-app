node {
    docker.image('node:16-buster-slim').inside('-p 3000:3000') {
        
        stage('Build') {
            echo 'Building the application...'
            sh 'npm install'
        }
        
        stage('Test') {
            echo 'Running tests...'
            sh './jenkins/scripts/test.sh'
        }
    }
}

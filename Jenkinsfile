pipeline {
    agent {
        docker {
            image 'monachus/hugo:v0.53'
            args '-v /root/nginx/html:/root/.jenkins/workspace/target/'
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'hugo' 
            }
        }
        stage('Rm') {
            steps {
                sh 'rm -rf /root/.jenkins/workspace/target/public'
            }
        }
        stage('Copy') {
            steps {
                sh 'cp -r /root/.jenkins/workspace/jovi-blog/public /root/.jenkins/workspace/target/'
            }
        }
        stage('Chmod') {
            steps {
                sh 'chmod -R 775 /root/.jenkins/workspace/target/public'
            }
        }
    }
}
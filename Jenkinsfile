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
                sh 'rm -rf /root/nginx/html/public'
            }
        }
        stage('Copy') {
            steps {
                sh 'cp -r /root/.jenkins/workspace/jovi-blog/public /root/nginx/html/'
            }
        }
    }
}
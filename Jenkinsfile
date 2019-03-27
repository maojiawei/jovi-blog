pipeline {
    agent {
        docker {
            image 'monachus/hugo:v0.53'
            args '-v /root/jenkins_build/jovi-blog:/root/.jenkins/workspace/target/'
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'hugo' 
            }
        }
        stage('Tar') {
            steps {
                sh 'tar -czvf public.tar.gz public'
            }
        }
        stage('Rm') {
            steps {
                sh 'rm /root/.jenkins/workspace/target/public.tar.gz'
            }
        }
        stage('Mkdir') {
            steps {
                sh 'mkdir -p /root/.jenkins/workspace/target'
            }
        }
        stage('Copy') {
            steps {
                sh 'cp /root/.jenkins/workspace/jovi-blog/public.tar.gz /root/.jenkins/workspace/target/'
            }
        }
    }
}
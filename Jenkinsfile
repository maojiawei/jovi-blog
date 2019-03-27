pipeline {
    agent {
        docker {
            image 'monachus/hugo:v0.53' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'hugo' 
            }
        }
    }
}
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo '소스코드 가져오기'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '빌드 수행'
                sh 'echo "build start"'
            }
        }

        stage('Test') {
            steps {
                echo '테스트 수행'
                sh 'echo "test start"'
            }
        }

        stage('Deploy') {
            steps {
                echo '배포 수행'
                sh 'echo "deploy start"'
            }
        }
    }
}

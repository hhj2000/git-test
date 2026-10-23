pipeline {
    agent any

    environment {
        IMAGE = 'my-app'
        TAG = '2ec4a2b'
    }

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
                dir('app') {
                    // 테스트 실패 시 다음 단계로 진행하지 않음
                    sh './gradlew test --no-daemon'
                }
            }

            post {
                always {
                    // 성공 여부와 관계없이 테스트 결과 수집
                    junit 'app/build/test-results/test/*.xml'
                }
            }
        }

        stage('Image') {
            steps {
                sh 'docker build -t "$IMAGE:$TAG" app'
            }
        }
    }
}

pipeline {
    agent any

    environment {
        IMAGE = 'harbor.skala-gj.com/skala-gj3/g104-devops-lab-app'
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
                // EKS의 x86_64 노드용 이미지 빌드
                sh 'docker build --platform linux/amd64 -t "$IMAGE:$TAG" app'
            }
        }

        stage('Push') {
            when {
                expression {
                    env.GIT_BRANCH == 'main' ||
                    env.GIT_BRANCH == 'origin/main' ||
                    env.BRANCH_NAME == 'main'
                }
            }

            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'harbor-login',
                        usernameVariable: 'REG_USER',
                        passwordVariable: 'REG_PASS'
                    )
                ]) {
                    sh '''
                        set +x
                        echo "$REG_PASS" | docker login harbor.skala-gj.com \
                            -u "$REG_USER" --password-stdin
                        docker push "$IMAGE:$TAG"
                    '''
                }
            }
        }
    }
}

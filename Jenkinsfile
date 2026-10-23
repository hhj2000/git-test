pipeline {
    agent any

    environment {
        IMAGE = 'harbor.skala-gj.com/skala-gj3/g104-devops-lab-app'
    }

    options {
        timeout(time: 20, unit: 'MINUTES')
        skipDefaultCheckout(true)
    }

    stages {
        stage('Checkout') {
            steps {
                echo '소스코드 가져오기'
                checkout scm

                script {
                    env.TAG = sh(
                        script: 'git rev-parse HEAD',
                        returnStdout: true
                    ).trim().take(7)
                }

                echo "이미지: ${env.IMAGE}:${env.TAG}"
            }
        }

        stage('Build') {
            steps {
                // 테스트는 다음 단계에서 실행
                sh './gradlew :app:assemble --no-daemon'
            }
        }

        stage('Test') {
            steps {
                // 루트의 Wrapper로 app 프로젝트 테스트 실행
                sh './gradlew :app:test --no-daemon'
            }

            post {
                always {
                    junit 'app/build/test-results/test/*.xml'
                }
            }
        }

        stage('Image') {
            steps {
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

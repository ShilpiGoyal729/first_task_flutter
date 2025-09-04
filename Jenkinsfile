pipeline {
    agent any

    environment {
        FLUTTER_HOME = '/opt/flutter'
        ANDROID_SDK_ROOT = '/opt/android-sdk'
        PATH = "${FLUTTER_HOME}/bin:${ANDROID_SDK_ROOT}/tools:${ANDROID_SDK_ROOT}/platform-tools:${env.PATH}"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Pull Docker Image') {
            steps {
                script {
                    try {
                        sh 'docker pull cirrusci/flutter:stable'
                    } catch (Exception e) {
                        echo 'Failed to pull cirrusci/flutter:stable, trying subosito/flutter'
                        sh 'docker pull subosito/flutter:latest'
                    }
                }
            }
        }

        stage('Flutter Build Inside Docker') {
            steps {
                script {
                    docker.image('subosito/flutter:latest').inside("--user root:root -v ${WORKSPACE}:${WORKSPACE}") {
                        sh '''
                            git config --global --add safe.directory ${WORKSPACE}
                            flutter doctor
                            flutter build apk --release
                        '''
                    }
                }
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/app-release.apk', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build and APK generation successful.'
        }
        failure {
            echo 'Build failed. Please check the logs.'
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Pull Docker Image') {
            steps {
                sh 'docker pull cirrusci/flutter:stable'
            }
        }

        stage('Flutter Build Inside Docker') {
            steps {
                script {
                    docker.image('cirrusci/flutter:stable').inside("--user root:root -v ${WORKSPACE}:${WORKSPACE}") {
                        sh '''
                            git config --global --add safe.directory ${WORKSPACE}
                            flutter doctor
                            flutter pub get
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
            echo 'Build failed. Check logs.'
        }
    }
}

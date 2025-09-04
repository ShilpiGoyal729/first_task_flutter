pipeline {
    agent any

    environment {
        ANDROID_HOME = "/opt/android-sdk-linux"
        PATH = "${env.PATH}:${ANDROID_HOME}/tools:${ANDROID_HOME}/tools/bin:${ANDROID_HOME}/platform-tools"
        FLUTTER_HOME = "/opt/flutter"
    }

    stages {

        stage('Pull Docker Image') {
            steps {
                echo "Pulling Flutter Docker image with Android SDK..."
                sh 'docker pull subosito/flutter:latest'
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Flutter Build Inside Docker') {
            steps {
                script {
                    // Get Jenkins user UID/GID
                    def uid = sh(script: 'id -u', returnStdout: true).trim()
                    def gid = sh(script: 'id -g', returnStdout: true).trim()

                    docker.image('subosito/flutter:latest').inside("-u ${uid}:${gid}") {
                        echo "Setting up Flutter environment..."

                        // Fix Git safe.directory locally
                        sh 'git config --global --add safe.directory ${WORKSPACE} || true'

                        // Accept Android licenses (non-interactive)
                        sh 'yes | flutter doctor --android-licenses || true'

                        // Flutter doctor check
                        sh 'flutter doctor -v'

                        // Get dependencies
                        sh 'flutter pub get'

                        // Build release APK
                        sh 'flutter build apk --release'
                    }
                }
            }
        }

        stage('Archive APK') {
            steps {
                echo "Archiving generated APK..."
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/*.apk', allowEmptyArchive: false
            }
        }
    }

    post {
        success { echo "Build succeeded!" }
        failure { echo "Build failed. Check logs!" }
    }
}

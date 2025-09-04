pipeline {
    agent any

    environment {
        ANDROID_HOME = "/opt/android-sdk-linux"
        PATH = "${env.PATH}:${ANDROID_HOME}/tools:${ANDROID_HOME}/tools/bin:${ANDROID_HOME}/platform-tools"
    }

    stages {

        stage('Pull Flutter Docker Image') {
            steps {
                echo "Pulling Flutter Docker image..."
                sh 'docker pull ghcr.io/cirruslabs/flutter:stable'
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
                    // Use Jenkins UID:GID to avoid permissions issues
                    def uid = sh(script: 'id -u', returnStdout: true).trim()
                    def gid = sh(script: 'id -g', returnStdout: true).trim()

                    docker.image('ghcr.io/cirruslabs/flutter:stable').inside("-u ${uid}:${gid}") {
                        echo "Configuring Flutter environment..."

                        // Fix Git ownership
                        sh 'git config --global --add safe.directory /sdks/flutter'

                        // Accept Android licenses to avoid build hang
                        sh 'yes | flutter doctor --android-licenses || true'

                        // Check Flutter setup
                        sh 'flutter --version'
                        sh 'flutter doctor -v'

                        // Get dependencies
                        sh 'flutter pub get'

                        // Build APK release
                        sh 'flutter build apk --release'
                    }
                }
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/*.apk', allowEmptyArchive: false
            }
        }
    }

    post {
        success { echo "✅ Build succeeded!" }
        failure { echo "❌ Build failed. Check logs." }
    }
}

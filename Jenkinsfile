pipeline {
    agent any
    environment {
        // Optional: Add environment variables if needed
        ANDROID_HOME = "/opt/android-sdk-linux"
        PATH = "${env.PATH}:${ANDROID_HOME}/tools:${ANDROID_HOME}/tools/bin:${ANDROID_HOME}/platform-tools"
    }
    stages {
        stage('Pull Flutter Docker Image') {
            steps {
                echo "Pulling Flutter Docker image..."
                sh '''
                    docker pull ghcr.io/cirruslabs/flutter:stable
                    docker images | grep flutter
                '''
            }
        }

        stage('Checkout Code') {
            steps {
                echo "Checking out source code..."
                checkout scm
            }
        }

        stage('Flutter Build Inside Docker') {
            steps {
                script {
                    // Run Flutter commands inside Docker
                    docker.image('ghcr.io/cirruslabs/flutter:stable').inside("-u 1000:1000 -v ${WORKSPACE}:${WORKSPACE}") {
                        echo "Setting up Flutter environment..."
                        // Fix Git dubious ownership issue
                        sh 'git config --global --add safe.directory /sdks/flutter'

                        // Verify Flutter
                        sh 'flutter --version'
                        sh 'flutter doctor -v'

                        // Get dependencies
                        sh 'flutter pub get'

                        // Build APK
                        sh 'flutter build apk --release'
                    }
                }
            }
        }

        stage('Archive APK') {
            steps {
                echo "Archiving the APK..."
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/*.apk', allowEmptyArchive: true
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline finished successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check logs."
        }
    }
}

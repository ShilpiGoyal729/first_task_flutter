pipeline {
    agent any

    options {
        timeout(time: 60, unit: 'MINUTES') // Prevents long-running interruption
    }

    stages {

        stage('Pull Flutter Docker Image') {
            steps {
                echo "Pulling Flutter Docker image..."
                sh 'docker pull ghcr.io/cirruslabs/flutter:stable'
                sh 'docker images | grep flutter'
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
                    docker.image('ghcr.io/cirruslabs/flutter:stable').inside {
                        echo "Setting up Flutter..."
                        sh 'flutter channel stable'
                        sh 'flutter upgrade'
                        sh 'flutter doctor -v || true'  // ignore warnings, continue build

                        echo "Installing dependencies..."
                        sh 'flutter pub get'
                        sh 'flutter pub upgrade'

                        echo "Building APK..."
                        sh 'flutter build apk --release'
                    }
                }
            }
        }

        stage('Archive APK') {
            steps {
                echo "Archiving APK artifacts..."
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/*.apk', allowEmptyArchive: false
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline finished successfully!'
        }
        failure {
            echo '❌ Pipeline failed! Check logs.'
        }
    }
}

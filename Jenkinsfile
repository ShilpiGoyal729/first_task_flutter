pipeline {
    agent any

    environment {
        FLUTTER_GIT_URL = 'https://github.com/flutter/flutter.git' 
        CHROME_EXECUTABLE = '/usr/bin/google-chrome'               
    }

    options {
        timeout(time: 60, unit: 'MINUTES') // prevents ClosedByInterruptException for long builds
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

        stage('Setup Flutter') {
            steps {
                echo "Verifying Flutter environment..."
                sh 'flutter channel stable'
                sh 'flutter upgrade'
                sh 'flutter doctor -v || true' // allow warnings, continue pipeline
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "Installing Flutter dependencies..."
                sh 'flutter pub get'
                sh 'flutter pub upgrade'
            }
        }

        stage('Build APK') {
            steps {
                echo "Building Flutter APK..."
                sh 'flutter build apk --release'
            }
        }

        stage('Archive Artifact') {
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

pipeline {
    agent any

    environment {
        // Flutter SDK path (make sure Jenkins has Flutter installed here)
        FLUTTER_HOME = "/var/lib/jenkins/flutter"
        PATH = "$FLUTTER_HOME/bin:$PATH"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'savio_branch_getx_fix', url: 'https://github.com/ShilpiGoyal729/first_task_flutter.git'
            }
        }

        stage('Dependencies') {
            steps {
                sh 'flutter pub get'
            }
        }

        stage('Build APK') {
            steps {
                sh 'flutter build apk --release'
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/app-release.apk', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "✅ Build and APK generation successful!"
        }
        failure {
            echo "❌ Build failed. Check logs."
        }
    }
}

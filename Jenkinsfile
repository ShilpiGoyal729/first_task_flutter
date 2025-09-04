pipeline {
    agent {
        docker {
            image 'cirrusci/flutter:latest'   // Flutter pre-installed
            args '-u root:root'               // run as root inside container
        }
    }

    stages {
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
        always {
            echo "✅ Build complete (or failed). Check artifacts."
        }
    }
}

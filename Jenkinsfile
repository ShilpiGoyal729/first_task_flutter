pipeline {
    agent {
        docker {
            image 'cirrusci/flutter:latest'   // Flutter pre-installed
            args '-u root:root'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'savio_branch_getx_fix',
                    url: 'https://github.com/ShilpiGoyal729/first_task_flutter.git'
            }
        }

        stage('Verify Flutter') {
            steps {
                sh 'flutter --version'
                sh 'flutter doctor'
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
        always {
            echo "✅ Pipeline finished. Check logs and artifacts."
        }
    }
}

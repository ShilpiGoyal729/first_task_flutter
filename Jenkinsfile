pipeline {
    agent any

    stages {
        stage('Checkout SCM') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/savio_branch_getx_fix']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/ShilpiGoyal729/first_task_flutter.git'
                    ]]
                ])
            }
        }

        stage('Setup Flutter') {
            steps {
                sh '''
                  echo "Cleaning any old Flutter SDK..."
                  rm -rf flutter

                  echo "Cloning Flutter SDK (stable branch)..."
                  git clone https://github.com/flutter/flutter.git -b stable

                  echo "Adding Flutter to PATH..."
                  export PATH=$PWD/flutter/bin:$PATH

                  echo "Flutter Version:"
                  flutter --version

                  echo "Flutter Doctor:"
                  flutter doctor -v
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                  export PATH=$PWD/flutter/bin:$PATH
                  echo "Running flutter pub get..."
                  flutter pub get
                '''
            }
        }

        stage('Build APK') {
            steps {
                sh '''
                  export PATH=$PWD/flutter/bin:$PATH
                  echo "Building APK..."
                  flutter build apk --release
                '''
            }
        }

        stage('Archive APK') {
            steps {
                echo "Archiving APK..."
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/*.apk', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            cleanWs()
            echo "Workspace cleaned."
        }
        success {
            echo "✅ Build completed successfully!"
        }
        failure {
            echo "❌ Build failed. Check logs."
        }
    }
}

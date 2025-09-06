pipeline {
    agent any

    environment {
        WORK_DIR = "${WORKSPACE}"
        ANDROID_HOME = "${WORKSPACE}/android-sdk"
        PATH = "${WORKSPACE}/flutter/bin:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${PATH}"
    }

    stages {

        stage('Setup Flutter') {
            steps {
                sh '''
                  if [ ! -d "${WORK_DIR}/flutter" ]; then
                    echo "Downloading Flutter SDK..."
                    git clone https://github.com/flutter/flutter.git -b stable
                  fi
                  flutter doctor || true
                '''
            }
        }

        stage('Setup Android SDK') {
            steps {
                sh '''
                  if [ ! -d "${ANDROID_HOME}" ]; then
                    echo "Downloading Android SDK..."
                    mkdir -p ${ANDROID_HOME}/cmdline-tools
                    curl -s https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -o cmdline-tools.zip
                  fi
                '''
                // Use Pipeline Utility Steps plugin to unzip
                unzip zipFile: 'cmdline-tools.zip', dir: "${ANDROID_HOME}/cmdline-tools"
                sh '''
                  mv ${ANDROID_HOME}/cmdline-tools/cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest || true
                  yes | sdkmanager --licenses || true
                  sdkmanager --install "platform-tools" "platforms;android-34" "build-tools;34.0.0" "ndk;27.0.12077973"
                '''
            }
        }

        stage('Get Dependencies') {
            steps {
                retry(2) {
                    timeout(time: 10, unit: 'MINUTES') {
                        sh 'flutter pub get'
                    }
                }
            }
        }

        stage('Build APK') {
            steps {
                sh '''
                  echo "Building release APK..."
                  ./flutter/bin/flutter build apk --release --verbose --no-daemon --stacktrace
                '''
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/app-release.apk', fingerprint: true
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

pipeline {
    agent any

    environment {
        ANDROID_HOME = "${env.WORKSPACE}/android-sdk"
        PATH = "${env.WORKSPACE}/flutter/bin:${env.ANDROID_HOME}/cmdline-tools/latest/bin:${env.ANDROID_HOME}/platform-tools:${env.ANDROID_HOME}/build-tools/34.0.0:${env.PATH}"
    }

    stages {
        stage('Setup Flutter') {
            steps {
                sh '''
                  # Remove old Flutter
                  rm -rf flutter

                  # Clone Flutter stable
                  git clone https://github.com/flutter/flutter.git -b stable
                  export PATH=$PWD/flutter/bin:$PATH
                  flutter --version
                '''
            }
        }

        stage('Setup Android SDK (Local)') {
            steps {
                sh '''
                  # Clean previous SDK
                  rm -rf $ANDROID_HOME
                  mkdir -p $ANDROID_HOME/cmdline-tools

                  # Download command-line tools
                  curl -o sdk.zip https://dl.google.com/android/repository/commandlinetools-linux-10406996_latest.zip
                  unzip -q sdk.zip -d $ANDROID_HOME/cmdline-tools
                  mv $ANDROID_HOME/cmdline-tools/cmdline-tools $ANDROID_HOME/cmdline-tools/latest
                  rm sdk.zip

                  # Accept licenses
                  yes | sdkmanager --licenses

                  # Install required components
                  sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"
                '''
            }
        }

        stage('Build Flutter APK') {
            steps {
                sh '''
                  export PATH=$PWD/flutter/bin:$PATH
                  flutter pub get
                  flutter build apk --release
                '''
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/*.apk', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

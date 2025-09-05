pipeline {
    agent any

    environment {
        ANDROID_HOME = "/opt/android-sdk"
        PATH = "$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"
    }

    stages {
        stage('Setup Flutter') {
            steps {
                sh '''
                  # Remove old Flutter if exists
                  rm -rf flutter
                  
                  # Download Flutter SDK
                  git clone https://github.com/flutter/flutter.git -b stable
                  export PATH=$PWD/flutter/bin:$PATH
                  
                  flutter --version
                '''
            }
        }

        stage('Setup Android SDK') {
            steps {
                sh '''
                  # Install Android SDK if not already installed
                  mkdir -p /opt/android-sdk/cmdline-tools
                  cd /opt/android-sdk/cmdline-tools

                  if [ ! -d "latest" ]; then
                    curl -s https://dl.google.com/android/repository/commandlinetools-linux-10406996_latest.zip -o cmdline-tools.zip
                    unzip -q cmdline-tools.zip -d .
                    mv cmdline-tools latest
                  fi

                  # Accept licenses
                  yes | sdkmanager --licenses
                  
                  # Install platforms & build-tools
                  sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"
                '''
            }
        }

        stage('Build APK') {
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
}

pipeline {
    agent any

    environment {
        TOOLS_DIR = "${WORKSPACE}/tools"
        PATH = "${WORKSPACE}/flutter/bin:${TOOLS_DIR}:${WORKSPACE}/android-sdk/cmdline-tools/latest/bin:${WORKSPACE}/android-sdk/platform-tools:${PATH}"
        ANDROID_HOME = "${WORKSPACE}/android-sdk"
    }

    stages {
        stage('Setup Tools') {
            steps {
                sh '''
                  mkdir -p $TOOLS_DIR

                  # Download portable curl if not exists
                  if [ ! -f "$TOOLS_DIR/curl" ]; then
                    echo "Downloading portable curl..."
                    wget -O $TOOLS_DIR/curl https://github.com/moparisthebest/static-curl/releases/download/v7.87.0/curl-amd64 || true
                    chmod +x $TOOLS_DIR/curl
                  fi

                  # Download portable unzip if not exists
                  if [ ! -f "$TOOLS_DIR/unzip" ]; then
                    echo "Downloading portable unzip..."
                    wget -O $TOOLS_DIR/unzip https://github.com/jeremysimmons/standalone-unzip/releases/download/v6.0/unzip-linux-x86_64 || true
                    chmod +x $TOOLS_DIR/unzip
                  fi

                  $TOOLS_DIR/curl --version || true
                  $TOOLS_DIR/unzip -v || true
                '''
            }
        }

        stage('Setup Flutter') {
            steps {
                sh '''
                  if [ ! -d "${WORKSPACE}/flutter" ]; then
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
                    $TOOLS_DIR/curl -s https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -o cmdline-tools.zip
                    $TOOLS_DIR/unzip cmdline-tools.zip -d ${ANDROID_HOME}/cmdline-tools
                    mv ${ANDROID_HOME}/cmdline-tools/cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest
                  fi

                  yes | sdkmanager --licenses || true
                  sdkmanager --install "platform-tools" "platforms;android-34" "build-tools;34.0.0" "ndk;27.0.12077973"
                '''
            }
        }

        stage('Get Dependencies') {
            steps {
                sh '''
                  flutter pub get
                '''
            }
        }

        stage('Build APK') {
            steps {
                sh '''
                  echo "Building release APK..."
                  flutter build apk --release
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

pipeline {
  agent any

  environment {
    IMAGE_NAME = "flutter-build-env:latest"
    APK_OUTPUT = "build/app/outputs/flutter-apk/app-release.apk"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/your-username/flutter-first-task.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          sh '''
            echo "Building Docker image..."
            docker build -t $IMAGE_NAME .
          '''
        }
      }
    }

    stage('Build APK inside Docker') {
      steps {
        script {
          sh '''
            docker run --rm \
              -v $WORKSPACE:/workspace \
              -w /workspace \
              $IMAGE_NAME \
              bash -c "flutter pub get && flutter build apk --release"
          '''
        }
      }
    }

    stage('Archive APK') {
      steps {
        archiveArtifacts artifacts: "${APK_OUTPUT}", fingerprint: true
      }
    }
  }

  post {
    always {
      cleanWs()
    }
  }
}

pipeline {
    agent any

    environment {
        FLUTTER_IMAGE = 'cirrusci/flutter:stable'
    }

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

        stage('Pull Docker Image') {
            steps {
                script {
                    echo "Pulling Flutter Docker Image..."
                    sh "docker pull ${FLUTTER_IMAGE}"
                }
            }
        }

        stage('Flutter Build Inside Docker') {
            steps {
                script {
                    echo "Building Flutter project inside Docker..."
                    docker.image(FLUTTER_IMAGE).inside {
                        sh """
                            flutter pub get
                            flutter build apk --release
                        """
                    }
                }
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
            echo "Workspace cleaned."
        }
        success {
            echo "Build completed successfully!"
        }
        failure {
            echo "Build failed. Check logs."
        }
    }
}

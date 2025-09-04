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
                    // Mount workspace automatically, set working directory
                    docker.image(FLUTTER_IMAGE).inside("-w ${env.WORKSPACE}") {
                        sh """
                            echo "Flutter Version:"
                            flutter --version
                            echo "Flutter Doctor:"
                            flutter doctor -v
                            echo "Current Directory:"
                            pwd
                            echo "Listing files:"
                            ls -la

                            echo "Accepting Android licenses..."
                            yes | flutter doctor --android-licenses

                            echo "Running flutter pub get..."
                            flutter pub get

                            echo "Building APK..."
                            flutter build apk --release
                        """
                    }
                }
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
            echo "Build completed successfully!"
        }
        failure {
            echo "Build failed. Check logs."
        }
    }
}

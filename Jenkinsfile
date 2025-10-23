pipeline {
    agent none

    stages {
        stage('Checkout and Build on Linux') {
            agent { label 'linux-unreal' }
            tools { git 'Git-Linux' }
            environment {
                PROJECT_DIR = "${env.WORKSPACE}/CICDTest"
                BUILD_OUTPUT = "${env.WORKSPACE}/BuildOutput/Linux"
                UAT_PATH = "/opt/UnrealEngine/Engine/Build/BatchFiles/RunUAT.sh"
            }
            steps {
                echo "Pulling latest code from GitHub..."
                checkout scm

                echo "Building Linux version..."
                sh '''
                    mkdir -p "$BUILD_OUTPUT"
                    "$UAT_PATH" BuildCookRun \
                        -project="$PROJECT_DIR/CICDTest.uproject" \
                        -noP4 \
                        -platform=Linux \
                        -clientconfig=Development \
                        -serverconfig=Development \
                        -cook -build -stage -pak -archive \
                        -archivedirectory="$BUILD_OUTPUT"
                '''
            }
            post {
                success {
                    archiveArtifacts artifacts: 'BuildOutput/Linux/**/*', fingerprint: true
                }
            }
        }

        stage('Checkout and Build on Windows') {
            agent { label 'windows' }
            tools { git 'Git-Windows' }
            environment {
                PROJECT_DIR = "${env.WORKSPACE}\\CICDTest"
                BUILD_OUTPUT = "${env.WORKSPACE}\\BuildOutput\\Windows"
                UAT_PATH = "C:\\Program Files\\Epic Games\\UE_5.4\\Engine\\Build\\BatchFiles\\RunUAT.bat"
            }
            steps {
                echo "Pulling latest code from GitHub..."
                checkout scm

                echo "Building Windows version..."
                bat """
                    if not exist "${BUILD_OUTPUT}" mkdir "${BUILD_OUTPUT}"
                    "${UAT_PATH}" BuildCookRun ^ 
                        -project="${PROJECT_DIR}\\CICDTest.uproject" ^ 
                        -noP4 ^ 
                        -platform=Win64 ^ 
                        -clientconfig=Development ^ 
                        -serverconfig=Development ^ 
                        -cook -build -stage -pak -archive ^ 
                        -archivedirectory="${BUILD_OUTPUT}"
                """
            }
            post {
                success {
                    archiveArtifacts artifacts: 'BuildOutput/Windows/**/*', fingerprint: true
                }
            }
        }
    }

    post {
        always {
            echo "Build pipeline finished"
        }
    }
}

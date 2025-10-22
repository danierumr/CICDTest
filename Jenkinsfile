pipeline {
    agent none

    stages {

        stage('Build on Linux') {
            agent { label 'linux-unreal' }
            environment {
                PROJECT_DIR = "/home/builduser/workspace/CICDTest_Build/CICDTest"
                BUILD_OUTPUT = "/home/builduser/workspace/CICDTest_Build/BuildOutput/Linux"
                UAT_PATH = "/opt/UnrealEngine/Engine/Build/BatchFiles/RunUAT.sh"
            }
            steps {
                echo "Checking out repo on Linux..."
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/danierumr/CICDTest.git']],
                    gitTool: 'Git-Linux'
                ])

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
                success { archiveArtifacts artifacts: 'BuildOutput/Linux/**/*', fingerprint: true }
            }
        }

        stage('Build on Windows') {
            agent { label 'windows' }
            environment {
                PROJECT_DIR = "C:\\Jenkins\\workspace\\CICDTest_Build\\CICDTest"
                BUILD_OUTPUT = "C:\\Jenkins\\workspace\\CICDTest_Build\\BuildOutput\\Windows"
                UAT_PATH = "C:\\Program Files\\Epic Games\\UE_5.4\\Engine\\Build\\BatchFiles\\RunUAT.bat"
            }
            steps {
                echo "Checking out repo on Windows..."
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/danierumr/CICDTest.git']],
                    gitTool: 'Git-Windows'
                ])

                echo "Building Windows version..."
                bat """
                mkdir "$BUILD_OUTPUT"
                "$UAT_PATH" BuildCookRun ^
                    -project="$PROJECT_DIR\\CICDTest.uproject" ^
                    -noP4 ^
                    -platform=Win64 ^
                    -clientconfig=Development ^
                    -serverconfig=Development ^
                    -cook -build -stage -pak -archive ^
                    -archivedirectory="$BUILD_OUTPUT"
                """
            }
            post {
                success { archiveArtifacts artifacts: 'BuildOutput/Windows/**/*', fingerprint: true }
            }
        }

    }

    post {
        always {
            echo "Build pipeline finished"
        }
    }
}

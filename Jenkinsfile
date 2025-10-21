pipeline {
    agent none   // define agent per stage

    stages {
        stage('Checkout') {
            agent any
            steps {
                echo "Checking out repo..."
                checkout scm
            }
        }

        stage('Build on Linux') {
            agent { label 'linux' }
            when { expression { isUnix() } }
            environment {
                PROJECT_DIR = "/var/jenkins_home/workspace/CICDTest_Build/CICDTest"
                BUILD_OUTPUT = "/var/jenkins_home/workspace/CICDTest_Build/BuildOutput/Linux"
                UAT_PATH = "/home/danierumr/Documents/Projects/UnrealEngine/Engine/Build/BatchFiles/RunUAT.sh"
            }
            steps {
                echo "Building Linux version..."
                sh '''
                mkdir -p $BUILD_OUTPUT
                $UAT_PATH BuildCookRun \
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
            when { expression { !isUnix() } }
            environment {
                PROJECT_DIR = "C:\\Jenkins\\workspace\\CICDTest_Build\\CICDTest"
                BUILD_OUTPUT = "C:\\Jenkins\\workspace\\CICDTest_Build\\BuildOutput\\Windows"
                UAT_PATH = "C:\\Program Files\\Epic Games\\UE_5.4\\Engine\\Build\\BatchFiles\\RunUAT.bat"
            }
            steps {
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

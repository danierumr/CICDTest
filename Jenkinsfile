pipeline {

    agent any

    environment {
        PROJECT_DIR = "${WORKSPACE}\\CICDTest"
        UAT_PATH = "C:\\Program Files\\Epic Games\\UE_5.4\\Engine\\Build\\BatchFiles\\RunUAT.bat"
        BUILD_PATH = "${WORKSPACE}\\BuildOutput"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Unreal Project') {
            steps {
                bat '''
                echo === Starting Unreal Build ===
                if exist "%BUILD_PATH%" rmdir /s /q "%BUILD_PATH%"
                call "%UAT_PATH%" BuildCookRun ^
                    -project="%PROJECT_DIR%\\CICDTest.uproject" ^
                    -noP4 -platform=Win64 -clientconfig=Development ^
                    -cook -build -stage -pak -archive ^
                    -archivedirectory="%BUILD_PATH%"
                '''
            }
        }

        stage('Archive Build') {
            steps {
                archiveArtifacts artifacts: 'BuildOutput/**/*', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "Build succeeded!"
        }
        failure {
            echo "Build failed!"
        }
    }

}
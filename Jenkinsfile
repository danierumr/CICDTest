pipeline {
    agent none

    stages {

        stage('Checkout and Build on Linux') {
            agent { label 'linux-unreal' }
            environment {
                PROJECT_DIR = "${env.WORKSPACE}/CICDTest"
                BUILD_OUTPUT = "${env.WORKSPACE}/BuildOutput/Linux"
                UAT_PATH = "/opt/UnrealEngine/Engine/Build/BatchFiles/RunUAT.sh"
            }
            steps {
                echo "Ensuring node-specific Git is used on Linux..."
                script {
                    // resolve the Git tool configured in Jenkins for the Linux node
                    def gitHome = tool name: 'Git-Linux', type: 'git'
                    env.PATH = "${gitHome}/bin:${env.PATH}"
                }

                echo "Checking out repo on Linux..."
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/danierumr/CICDTest.git',
                    ]]
                    // removed gitTool here; PATH now points to the node's Git installation
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
                success {
                    archiveArtifacts artifacts: 'BuildOutput/Linux/**/*', fingerprint: true
                }
            }
        }

        stage('Checkout and Build on Windows') {
            agent { label 'windows' }
            environment {
                PROJECT_DIR = "${env.WORKSPACE}\\CICDTest"
                BUILD_OUTPUT = "${env.WORKSPACE}\\BuildOutput\\Windows"
                UAT_PATH = "C:\\Program Files\\Epic Games\\UE_5.4\\Engine\\Build\\BatchFiles\\RunUAT.bat"
            }
            steps {
                echo "Ensuring node-specific Git is used on Windows..."
                script {
                    // resolve the Git tool configured in Jenkins for the Windows node
                    def gitHome = tool name: 'Git-Windows', type: 'git'
                    // prepend the git bin directory to PATH so checkout uses the node's Git
                    env.PATH = "${gitHome}\\bin;${env.PATH}"
                }

                echo "Checking out repo on Windows..."
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/danierumr/CICDTest.git',
                    ]]
                    // removed gitTool here; PATH now points to the node's Git installation
                ])

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

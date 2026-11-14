
pipeline {

    agent any

    stages {

        stage('Build') {
            steps {

                echo 'Building the project...'

                bat '''
                    @echo off

                    echo ===== INSTALLING DEPENDENCIES =====

                    docker run --rm ^
                      -v "%WORKSPACE%:/app" ^
                      -w /app ^
                      node:18-alpine ^
                      sh -c "npm ci"

                    if errorlevel 1 exit /b 1

                    echo ===== BUILDING PROJECT =====

                    docker run --rm ^
                      -v "%WORKSPACE%:/app" ^
                      -w /app ^
                      node:18-alpine ^
                      sh -c "npm run build"

                    if errorlevel 1 exit /b 1

                    echo ===== BUILD OUTPUT =====

                    if not exist build\\index.html (
                        echo ERROR: Build output not found!
                        exit /b 1
                    )

                    dir build

                    echo BUILD COMPLETED SUCCESSFULLY!
                '''
            }
        }
    }

    post {
        always {
            echo 'Build process finished!'
        }

        success {
            echo 'SUCCESS: Project built successfully!'
        }

        failure {
            echo 'FAILURE: Project build failed!'
        }
    }
}

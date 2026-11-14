
pipeline {

    agent any

    stages {

        // BUILD STAGE
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

                    echo ===== VERIFYING BUILD =====

                    if not exist build\\index.html exit /b 1

                    echo Build completed successfully!
                '''
            }
        }

        // TEST STAGE
        stage('Test') {
            steps {

                echo 'Running application tests...'

                bat '''
                    @echo off

                    echo ===== VERIFYING BUILD ARTIFACT =====

                    if not exist build\\index.html (
                        echo ERROR: index.html not found!
                        exit /b 1
                    )

                    echo Build artifact exists!

                    echo ===== RUNNING AUTOMATED TESTS =====

                    docker run --rm ^
                      -v "%WORKSPACE%:/app" ^
                      -w /app ^
                      -e CI=true ^
                      node:18-alpine ^
                      sh -c "npm test -- --watchAll=false"

                    if errorlevel 1 exit /b 1

                    echo ===== TESTS COMPLETED =====
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished!'
        }

        success {
            echo 'SUCCESS: Build and tests completed!'
        }

        failure {
            echo 'FAILURE: Build or tests failed!'
        }
    }
}

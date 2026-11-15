
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
                '''
            }
        }

        // TEST STAGE
        stage('Test') {
            steps {

                echo 'Running automated tests...'

                bat '''
                    @echo off

                    echo ===== RUNNING TESTS =====

                    docker run --rm ^
                      -v "%WORKSPACE%:/app" ^
                      -w /app ^
                      -e CI=true ^
                      node:18-alpine ^
                      sh -c "npm test -- --watchAll=false"

                    if errorlevel 1 exit /b 1

                    echo ===== CHECKING JUNIT REPORT =====

                    if not exist test-results\\junit.xml (
                        echo ERROR: JUnit report not found!
                        exit /b 1
                    )

                    echo JUnit report generated successfully!

                    dir test-results
                '''
            }
        }


        stage('Deploy') {

             steps {

                echo 'Installing Netlify CLI...'

                 bat '''
                         @echo off

                        echo ===== INSTALLING NETLIFY CLI =====

                        docker run --rm ^
                        -v "%WORKSPACE%:/app" ^
                        -w /app ^
                        node:22-alpine ^
                        sh -c "node --version && npm install --no-save --no-package-lock netlify-cli && ./node_modules/.bin/netlify --version"

                        if errorlevel 1 exit /b 1

                            echo ===== NETLIFY CLI INSTALLED =====
                        '''
             }
        }

    }

    // PUBLISH JUNIT REPORT
    post {

        always {
            junit 'test-results/junit.xml'
        }

        success {
            echo 'SUCCESS: Build and tests completed!'
        }

        failure {
            echo 'FAILURE: Build or tests failed!'
        }
    }
}

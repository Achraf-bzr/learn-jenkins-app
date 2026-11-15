
    pipeline {

        agent any

        environment {
            NETLIFY_SITE_ID = '5b03e458-cc80-4395-b624-2213a1c57f3e' 
            NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        }

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

                    echo '===== INSTALLING NETLIFY CLI ====='

                    // Verify and prepare Netlify CLI
                   bat '''
                  @echo off

                  docker run --rm ^
                  -v "%WORKSPACE%:/app" ^
                  -w /app ^
                  node:22-alpine ^
                  sh -c "mkdir -p netlify-cli-tools && cd netlify-cli-tools && if [ ! -x node_modules/.bin/netlify ]; then npm init -y && npm install --save-exact netlify-cli@27.19.0; fi && ./node_modules/.bin/netlify --version"

                  if errorlevel 1 exit /b 1
                  '''

                  echo '===== DEPLOYING TO NETLIFY PRODUCTION ====='

                    // Production deployment
                bat '''
                 @echo off

                 docker run --rm ^
                 -e NETLIFY_AUTH_TOKEN ^
                 -e NETLIFY_SITE_ID ^
                 -v "%WORKSPACE%:/app" ^
                 -w /app/netlify-cli-tools ^
                 node:22-alpine ^
                 sh -c "./node_modules/.bin/netlify deploy --prod --dir=/app/build --site=\\"$NETLIFY_SITE_ID\\""

                 if errorlevel 1 exit /b 1

                  echo ===== PRODUCTION DEPLOYMENT COMPLETED =====
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

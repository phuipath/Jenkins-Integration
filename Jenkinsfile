pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github_token') // Jenkins credential ID for your classic GitHub token
        UIPATH_CLIENT_ID = credentials('UIPATH_CLIENT_ID')
        UIPATH_CLIENT_SECRET = credentials('UIPATH_CLIENT_SECRET')
        UIPATH_ORCH_URL = 'https://staging.uipath.com' // Update if different
        TEST_SET_NAME = 'Jenkins USPS Informed Delivery'
	UIPATH_TEST_MANAGER_URL = 'https://staging.uipath.com/uibank/AutomationCoE/testmanager_/api/v2/'
    }

    stages {
        stage('Checkout Repository') {
            steps {

                         echo 'Checking out repository from GitHub...'
                    checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    userRemoteConfigs: [[
                        url: 'https://github.com/phuipath/Jenkins-Integration.git',
                        credentialsId: "${env.GITHUB_CREDENTIALS}"
                    ]]
                ])
                    } 

               
            }

        stage('Authenticate with UiPath') {
            steps {
                script {
                    echo 'Authenticating with UiPath...'
                    bat """
                    curl -s -X POST "${env.UIPATH_ORCH_URL}/identity_/connect/token" ^
                        -H "Content-Type: application/x-www-form-urlencoded" ^
                        -d "grant_type=client_credentials&client_id=${env.UIPATH_CLIENT_ID}&client_secret=${env.UIPATH_CLIENT_SECRET}&scope=TM.TestSets TM.TestExecutions" ^
                        > token.json
                    """
                    def tokenJson = readJSON file: 'token.json'
                    env.UIPATH_ACCESS_TOKEN = tokenJson.access_token
                    echo 'Obtained UiPath token.'
                }
            }
        }



        stage('Start Test Set Execution v2') {
		
            steps {
                script {
                    echo "Starting Test Set Execution..."
                    bat """
                    curl -s -X POST "${env.UIPATH_TEST_MANAGER_URL}aff5a971-3c74-0000-2a81-0b471f7c0aee/testsets/95c81b31-7ad2-0200-4fc9-0b48e58cb559/startexecute?executionType=automated" ^
                        -H "Content-Type: application/json" ^
                        -H "Authorization: Bearer ${env.UIPATH_ACCESS_TOKEN}" > execution.json
                    """
                    def execJson = readJSON file: 'execution.json'
                    env.EXECUTION_ID = execJson.Id
                    echo "Started execution: ${env.EXECUTION_ID}"
                }
            }
        }

        stage('Wait for Completion') {
            steps {
                script {
                    echo "Waiting for execution to complete..."
                    // Optional: add polling here or just let it run asynchronously
                }
            }
        }
	}
    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Execution succeeded!'
        }
        failure {
            echo 'Execution failed!'
        }
    }
}


pipeline {
    parameters {
        choice(name: 'DEPLOYMENT_TYPE', choices: ['docker-compose.postman.yml', 'docker-compose.postman_concurrency.yml', 'docker-compose.postman_reliability.yml'], description: 'Deployment Type')

        string(name: 'TEST_ENV_URL', defaultValue: 'http://devowelizer:8080', description: 'Target url')
        choice(name: 'TEST_SUITE', choices: ['data-driven.json', 'local.json'], description: 'Test Suite')
        choice(name: 'TEST_DATA', choices: ['basic.csv', 'example_text.csv', 'basic_lowercase.csv', 'basic_uppercase.csv', 'character_types_encoded_utf8.json', 'character_types_unicode.json', 'special_characters.csv', 'special_vowels.csv'], description: 'Test Suite')
        choice(name: 'TEST_ENV', choices: ['docker-internal.json', 'local.json'], description: 'Test Environment')
        string(name: 'REQUEST_DELAY', defaultValue: '0', description: 'Request Delay in MS')
        string(name: 'ITERATION_COUNT', defaultValue: '1', description: 'Test Iteration Count')

        booleanParam(name: 'IS_SLACK', defaultValue: true, description: 'Send Slack message')
        booleanParam(name: 'IS_SLACK_UPLOAD', defaultValue: true, description: 'Upload Reports to Slack')
        booleanParam(name: 'IS_SLACK_UPLOAD_SUCCESS', defaultValue: false, description: 'Upload on Success too')
        string(name: 'SLACK_CHANNEL', defaultValue: 'example', description: 'Slack Channel')
        string(name: 'SLACK_CREDENTIAL_ID', defaultValue: 'example', description: 'Slack Credential ID')

    }
    agent {
        node {
            label 'master'
        }
    }
    triggers {
        cron('H 9-17/8 * * 1-5')
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    environment {
        TEST_ENV_URL= "${params.TEST_ENV_URL}"
        TEST_SUITE = "${params.TEST_SUITE}"
        TEST_ENV = "${params.TEST_ENV}"
        TEST_DATA = "${params.TEST_DATA}"
        REQUEST_DELAY = "${params.REQUEST_DELAY}"
        ITERATION_COUNT = "${params.ITERATION_COUNT}"
    }
    stages {
        stage('Build Image'){
            steps{
                sh "docker-compose -f ${params.DEPLOYMENT_TYPE}.yml build"
            }
        }
        stage('Deploy Image'){
            steps{
                sh "docker-compose pull"
                sh "docker-compose up -d"
            }
        }
        stage('Tests'){
            steps{
                sh "docker-compose -f ${params.DEPLOYMENT_TYPE} up --exit-code-from devowelizer-qa"
            }

        }

    }
    post {
        failure {
            script {
                if (params.IS_SLACK) {

                    slackSend channel: "${params.SLACK_CHANNEL}", color: '#E01563',botUser: true, failOnError: true, message: "API Automation [${env.BRANCH_NAME}] - ${params.TEST_ENV_URL} - Test Failure :no_entry: Build:${env.BUILD_NUMBER} \n Collection: `${params.TEST_SUITE}`", teamDomain: 'example', tokenCredentialId: "${params.SLACK_CREDENTIAL_ID}"

                    try {
                        if(params.IS_SLACK_UPLOAD){
                            slackUploadFile channel: "${params.SLACK_CHANNEL}", credentialId: "${params.SLACK_CREDENTIAL_ID}", filePath: 'results/*.html', initialComment: 'API Testing Reports'
                        }
                    }catch(Exception e){
                        echo e.toString()
                    }

                    sh 'git clean -fdx'
                    cleanWs()
                }
            }

        }
        success {
            script {
                if (params.IS_SLACK) {

                    slackSend channel: "${params.SLACK_CHANNEL}", color: '#3EB991',botUser: true, failOnError: true, message: "API Automation [${env.BRANCH_NAME}] - ${params.TEST_ENV_URL} - Test Passed :heavy_check_mark: Build:${env.BUILD_NUMBER} \n Collection: `${params.TEST_SUITE}`", teamDomain: 'example', tokenCredentialId: "${params.SLACK_CREDENTIAL_ID}"

                    try {
                        if(params.IS_SLACK_UPLOAD_SUCCESS){
                            slackUploadFile channel: "${params.SLACK_CHANNEL}", credentialId: "${params.SLACK_CREDENTIAL_ID}", filePath: 'results/*.html', initialComment: 'API Testing Reports'
                        }
                    }catch(Exception e){
                        echo e.toString()
                    }

                    sh 'git clean -fdx'
                    cleanWs()
                }
            }
        }
        always{
            script{
                try {
                    sh "docker-compose down || true"
                    sh "docker-compose -f ${params.DEPLOYMENT_TYPE} down || true"
                    archiveArtifacts artifacts: '**/results/*.html', fingerprint: true

                }catch (Exception e) {
                    echo e.toString()
                }
            }
        }
    }
}

pipeline{
    agent any

    environment{
        DOCKERHUB_USERNAME = "marcmael"
        APP_NAME = "gitops_argocd"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTERY_CREDS = "dockerhub-creds"

    }

    stages{
        stage("CLEANUP WORKSPACE"){
            steps{
                echo "========executing CLEANUP WORKSPACE ========"
                script{
                    cleanWs()
                }
            }
        }

        stage('GIT CHECKOUT'){
            steps{
                script{
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/marcmael1/gitops_argocd.git']])
                }
            }
        }

        stage('GITHUB'){
            steps{
                script{
                    git credentialsId: "github-token",
                    url: "https://github.com/marcmael1/gitops_argocd.git",
                    branch: 'main'
                }
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}
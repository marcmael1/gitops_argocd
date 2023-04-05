pipeline{
    agent any

    environment{
        DOCKERHUB_USERNAME = "marcmael"
        APP_NAME = "gitops-argocd"
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

        // stage('GITHUB'){
        //     steps{
        //         script{
        //             git credentialsId: "github-token",
        //             url: "https://github.com/marcmael1/gitops_argocd.git",
        //             branch: 'main'
        //         }
        //     }
        // }


        stage('BUILD DOCKER IMAGE'){
            steps{
                script{
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }

        stage('PUSH IMAGE TO DOCKERHUB'){
            steps{
                script{
                    docker.withRegistry('', REGISTERY_CREDS){
                        docker_image.push "${IMAGE_TAG}"
                        docker_image.push "latest"
                    }
                }
            }
        }

        stage('updating k8s deployment file - DEV'){
            steps{
                script{ 
                    sh ("""
                    cat deployment.yaml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                    cat deployment.yaml
                    """)  
                    
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
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
                    cat deployment.yml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                    cat deployment.yml
                    """)  
                    
                }
            }
        }

        stage('Push the new update deployment filed8f to github'){
            steps{
                script{
                    sh ("""
                    git show-ref
                    git pull https://github.com/marcmael1/gitops_argocd.git main
                    git config --global user.name "marcmael1"
                    git config --global user.email "marctchouanche@gmail.com"
                    git add deployment.yml
                    git commit -m "update deployment file to github"
                    """)
                    withCredentials([gitUsernamePassword(credentialsId: 'github-token', gitToolName: 'Default')]){
                        sh "git push https://github.com/marcmael1/gitops_argocd.git main"
                    }
                }
            }
        }
    }
}
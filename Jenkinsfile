pipeline{
    agent any

    stages{
        stage("CLEANUP WORKSPACE"){
            steps{
                echo "========executing CLEANUP WORKSPACE ========"
                script{
                    cleanWs()
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
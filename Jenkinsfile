pipeline {

    agent any

    environment{

        DOCKERHUB_USERNAME = "tchocothepierre"
        APP_NAME = "gitops-argo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }

    stages{

            stage('Cleanup Workspace'){
                
                steps{

                    script{

                        cleanWs()
                    }

                }

            }

            stage('Checkout SCM'){

                steps{

                    script{

                        git credentialsId : 'github',
                        url: 'https://github.com/tchocothepierre/gitops_argocd_project.git',
                        branch: 'main'

                    }
                }

            }

            stage('Build Docker Image'){    //using Docker function

                steps{

                    script{

                        docker_image = docker.build "${IMAGE_NAME}"

                    }
                }
            }

            stage('Push Docker Image'){     //using Docker function

                steps{

                    script{

                        docker.withRegistry('', REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    
                        }

                    }
                }
            }

            stage('Delete Docker Images'){

                steps{

                    script{

                        sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                        sh "docker rmi ${IMAGE_NAME}:latest"

                    }

                }
            }

            stage('Updating Kubernetes deployment'){

                steps{

                    script{
                        
                        sh """
                        cat deployment.yml
                        sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                        cat deployment.yml
                        """

                    }
                }
            }

    }

}




//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  github passwd token
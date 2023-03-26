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

             stage('trigger config change pipeline'){

                steps{

                    script{
                            
                        sh "curl -v -k --user jobby:11762150093642bee36671cee1b7bedae3 -X POST -H 'cache-control:no-cache' -H 'content-type:application/x-www-form-urlenconded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'http://100.24.6.182:8080/job/gitops_argocd_CD/buildWithParameters?token=gitops-config'"

                    }

                }

             }

    }

}


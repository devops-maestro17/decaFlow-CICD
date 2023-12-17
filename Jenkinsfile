pipeline {
    agent any

    environment{
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'latest', url: 'https://github.com/devops-maestro17/10-Tier-MicroService-Appliction.git'
            }
        }
        stage('Static Code Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=10-Tier -Dsonar.projectName=10-Tier -Dsonar.java.binaries=. '''
                }
            }
        }
        
        stage('Building adservice') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/adservice') {
                            sh 'docker build -t containerizeops/adservice:latest .'
                            sh 'docker push containerizeops/adservice:latest'
                            sh 'docker rmi containerizeops/adservice:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building cartservice') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/cartservice/src/') {
                            sh 'docker build -t containerizeops/cartservice:latest .'
                            sh 'docker push containerizeops/cartservice:latest'
                            sh 'docker rmi containerizeops/cartservice:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building checkoutservice') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/checkoutservice') {
                            sh 'docker build -t containerizeops/checkoutservice:latest .'
                            sh 'docker push containerizeops/checkoutservice:latest'
                            sh 'docker rmi containerizeops/checkoutservice:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building currencyservice') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/currencyservice') {
                            sh 'docker build -t containerizeops/currencyservice:latest .'
                            sh 'docker push containerizeops/currencyservice:latest'
                            sh 'docker rmi containerizeops/currencyservice:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building emailservice') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/emailservice') {
                            sh 'docker build -t containerizeops/emailservice:latest .'
                            sh 'docker push containerizeops/emailservice:latest'
                            sh 'docker rmi containerizeops/emailservice:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building frontend') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/frontend') {
                            sh 'docker build -t containerizeops/frontend:latest .'
                            sh 'docker push containerizeops/frontend:latest'
                            sh 'docker rmi containerizeops/frontend:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building loadgenerator') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/loadgenerator') {
                            sh 'docker build -t containerizeops/loadgenerator:latest .'
                            sh 'docker push containerizeops/loadgenerator:latest'
                            sh 'docker rmi containerizeops/loadgenerator:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building paymentservice') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/paymentservice') {
                            sh 'docker build -t containerizeops/paymentservice:latest .'
                            sh 'docker push containerizeops/paymentservice:latest'
                            sh 'docker rmi containerizeops/paymentservice:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building productcatalogservice') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/productcatalogservice') {
                            sh 'docker build -t containerizeops/productcatalogservice:latest .'
                            sh 'docker push containerizeops/productcatalogservice:latest'
                            sh 'docker rmi containerizeops/productcatalogservice:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building recommendationservice') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/recommendationservice') {
                            sh 'docker build -t containerizeops/recommendationservice:latest .'
                            sh 'docker push containerizeops/recommendationservice:latest'
                            sh 'docker rmi containerizeops/recommendationservice:latest'
                        }
                    }
                }
            }
        }
        
        stage('Building shippingservice') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/decaFlow-CICD-pipeline/src/shippingservice') {
                            sh 'docker build -t containerizeops/shippingservice:latest .'
                            sh 'docker push containerizeops/shippingservice:latest'
                            sh 'docker rmi containerizeops/shippingservice:latest'
                        }
                    }
                }
            }
        }
        
        stage('Deploy app to K8s') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'my-cluster', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://5CB0DE3BAD02D23BE9E183931E3274D1.gr7.ap-south-1.eks.amazonaws.com') {
                    sh 'kubectl apply -f deployment-service.yml'
                    sh 'kubectl get pods'
                    sh 'kubectl get svc'
                }
            }
        }
        
    }
}
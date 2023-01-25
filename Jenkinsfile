pipeline {
    agent {
        docker { image 'openjdk:17.0.2-jdk-bullseye' }
    }
    stages {
        stage("Show Working Branch") {
            steps {
                echo 'Building...' + env.BRANCH_NAME
            }
        }
        stage("Unit Test stage") {
            steps {
                sh "./mvnw test -Dsnyk.skip"
            }
        }
        stage("Security Test Stage") {
            steps {
                sh "./mvnw snyk:monitor"
            }
        }
        stage("Build") {
            steps {
                sh "./mvnw install -Dsnyk.skip"
            }
        }
        stage("Build Docker Image") {
            steps {
                sh "./mvnw spring-boot:build-image -Dsnyk.skip"
            }
        }
        stage("Push Docker image to Docker Hub") {
            environment {
                DOCKERHUB_COMMON_CREDS = credentials('dockerhub-common-creds')
                NAME="cardb"
                VERSION="${env.BUILD_ID}"
            }
            steps {
                sh "docker tag cardb:0.0.1-SNAPSHOT $DOCKERHUB_COMMON_CREDS_USR/$NAME:$VERSION"
                sh "docker login --username $DOCKERHUB_COMMON_CREDS_USR --password $DOCKERHUB_COMMON_CREDS_PSW"
                sh "docker push $DOCKERHUB_COMMON_CREDS_USR/$NAME:$VERSION"
            }
        }
        stage("Install AWS") {
            steps {
                sh '''
                    curl https://oss.oracle.com/el4/unzip/unzip.tar -o unzip.tar
                    tar -xf unzip.tar
                    chmod +x unzip
                    mv unzip /usr/local/bin/unzip
                    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                    unzip -o awscliv2.zip
                    ./aws/install

                '''
            }
        }
        stage("Push Docker Image to ECR") {
            environment {
                AWS_DEFAULT_REGION="us-east-2"
            }
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'training-aws-creds', secretKeyVariable:
                            'AWS_SECRET_ACCESS_KEY')]) {
                    sh "aws ecr get-login-password | docker login --username AWS --password-stdin 392405208147.dkr.ecr.us-east-2.amazonaws.com"
                    sh "docker tag cardb:0.0.1-SNAPSHOT 392405208147.dkr.ecr.us-east-2.amazonaws.com/cardb:latest"
                    sh "docker push 392405208147.dkr.ecr.us-east-2.amazonaws.com/cardb:latest"
                }
            }
        }
<<<<<<< HEAD
        stage ('Deploy in kubernetes'){
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'training-aws-creds', secretKeyVariable:
                            'AWS_SECRET_ACCESS_KEY')]){
                                 sh '''
                                        curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
                                        chmod +x kubectl
                                        mv kubectl /usr/local/bin/
                                        curl -LO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64
                                        chmod +x ./kubectl-argo-rollouts-linux-amd64
                                        mv kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts
                                        aws eks --region us-east-2 update-kubeconfig --name test-eks-oPmKw5Gs
                                    '''
                            }
            }
        }
=======
>>>>>>> 4b1a81fca07c3a467ab2b2e42d425b025bf4b09e
    }
}

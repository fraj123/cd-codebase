pipeline {
    agent {
        docker { image 'openjdk:17.0.2' }
    }
    stages {
        stage("Show Working Branch") {
            steps {
                echo 'Building...' + env.BRANCH_NAME
            }
        }
        stage('Code Coverage Test') {
            steps {
                sh "./mvnw clean install -Dsnyk.skip -DskipTests=true -Dmaven.test.failure.ignore=true sonar:sonar -Dsonar.projectKey=$env.SONAR_PROJECT -Dsonar.host.url=$env.SONAR_HOST -Dsonar.login=$env.SONAR_TOKEN"
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
            }
            steps {
                sh "docker tag cardb:0.0.1-SNAPSHOT $DOCKERHUB_COMMON_CREDS_USR/cardb"
                sh "docker login --username $DOCKERHUB_COMMON_CREDS_USR --password $DOCKERHUB_COMMON_CREDS_PSW"
                sh "docker push $DOCKERHUB_COMMON_CREDS_USR/cardb"
            }
        }
        stage("Deploy artifacts") {
            steps {
                sh "./mvnw deploy -Dsnyk.skip"
            }
        }
    }
}

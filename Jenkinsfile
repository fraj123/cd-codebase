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
        stage("Security Test Stage") {
            steps {
                sh "./mvnw snyk:monitor"
            }
        }
    }
}

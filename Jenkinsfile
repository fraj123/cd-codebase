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
        stage("Security Test Stage") {
            steps {
                sh "./mvnw snyk:test"
            }
        }
    }
}

pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY     = credentials('AWS_ACCESS_KEY')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

    stages {
        stage('Source code checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }

        stage('Terraform initialization') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Terraform plan generation') {
            steps {
                sh 'terraform plan -out=tfplan'
            }
        }

        // stage('Infrastructure deployment') {
        //     steps {
        //         sh 'terraform apply -input=false tfplan'
        //     }
        // }
    }

    post {
        always {
            cleanWs()
        }
    }
}
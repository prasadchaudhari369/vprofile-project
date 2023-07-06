pipeline {
    agent any 
    environment {
        registryCredential = 'ecr:us-east-1:admin'
        appRegistry = '017002368057.dkr.ecr.us-east-1.amazonaws.com/visualpath-webapp'
        visualpathRegistory = 'https://017002368057.dkr.ecr.us-east-1.amazonaws.com'
        cluster = 'visualpath'
        service = 'visualpath-service'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/prasadchaudhari369/vprofile-project.git'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Integration Test') {
            steps {
                sh 'mvn verify -DskipTests'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Sonarqube Analysis') {
            steps{
                withSonarQubeEnv(credentialsId: 'sqa_4d2e36cc2e012af5b269c8ca6e11388bc557e894', installationName: 'sonar'){
                    sh 'mvn clean package sonar:sonar'
                }
            }
        }

        stage('Build docker image'){
            steps {
                script {
                    dockerImage = docker.build(appRegistry+":v1", ".")
                }

            }
        }

        stage('Upload the image on ECR') {
            steps {
                script {
                    docker.withRegistry(visualpathRegistory, registryCredential) {
                        dockerImage.push("v1")
                    }
                    
                }
            }
        }

        stage('Deploy on ECS') {
            steps {
                withAWS(credentials:'admin', region: 'us-east-1'){
                    sh 'aws ecs update-service --cluster ${cluster} --service $(service) --force-new-deployement'
                }
            }
        }
    }
}

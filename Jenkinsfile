pipeline {
    agent any 
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-jenkins')
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
        stage('NexusArtifactUploader'){
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'my.nexus.address',
                    groupId: 'com.example',
                    version: version,
                    repository: 'RepositoryName',
                    credentialsId: 'CredentialsId',
                    artifacts: [
                        [artifactId: projectName,
                        classifier: '',
                        file: 'my-service-' + version + '.jar',
                        type: 'jar']
                    ]
                )
            }
        }

        stage('Build The Image'){
            steps {
                sh 'docker build -t prasadchaudhari369/visualpath:v1 .'

            }
        }

        stage('Docker_Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push') {
            steps {
                sh 'docker push prasadchaudhari369/visualpath:v1'
            }
        }

         

    }

}

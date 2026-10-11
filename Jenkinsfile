pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/nexqor/hotstar-cicd-pipeline-.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Scanning') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=hotstar'
                }
            }
        }

        stage('Artifacts') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('deploy to tomcat') {
            steps {
                deploy(
                    adapters: [
                        tomcat9(
                            alternativeDeploymentContext: '',
                            credentialsId: 'tomcat-creds',
                            path: '',
                            url: 'http://192.168.47.129:8081/'
                        )
                    ],
                    contextPath: 'webapp',
                    war: 'target/myapp.war'
                )
            }
        }

        stage('artifacts to nexus') {
            steps {
                nexusArtifactUploader(
                    artifacts: [[
                        artifactId: 'myapp',
                        classifier: '',
                        file: 'target/myapp.war',
                        type: 'war'
                    ]],
                    credentialsId: 'nexus',
                    groupId: 'io.github.nexqor',
                    nexusUrl: '192.168.47.129:8082/',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: 'hotstar',
                    version: '8.3.3-SNAPSHOT'
                )
            }
        }
    }
}

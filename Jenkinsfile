  
pipeline {
    agent any   
    environment {
                    registry = "shilpabains/first-webapp1"
                    registryCredential = 'DockerHub'
                    dockerImage = ''
                 }
    stages {
        stage('Fetch')
        {
            steps
            {
                git url : "https://github.com/Shilpa40/MavenappSourceCode.git"
            }
        }
        stage('Build')
        {
            steps
            {
                echo 'Hello World'
        echo 'Building.....'
                bat 'mvn clean install'
            }
        }
        stage('Unit Test')
        {
            steps
            {
        echo 'Testing....'
                bat 'mvn test'
            }
        }
        stage('Sonar Analysis')
        {
            steps
            {
        echo 'Sonar Analysis....'
                withSonarQubeEnv("SonarQube")
                {
                    bat "mvn sonar:sonar"
                }  
            }
        }
        stage('Upload to Artifactory')
        {
            steps
            {
            echo 'Uploading....'
                rtMavenDeployer (
                    id: 'deployer-unique-id',
                    serverId: 'Artifactory',
                    releaseRepo: 'example-repo-local',
                    snapshotRepo: 'example-repo-local'
                )
                rtMavenRun (
                pom: 'pom.xml',
                goals: 'clean install',
                deployerId: 'deployer-unique-id'
                )
                rtPublishBuildInfo (
                    serverId: 'Artifactory'
                        )
            }
        }
        stage('Building our image') {
                        steps{
                        script {
                            bat "docker build -t devops:${BUILD_NUMBER} ."
                        }
                      }
                   }
       stage ("Docker Deployment")
        {
        steps
        {
        bat "docker run --name shilpa -d -p 9050:8080 devops:${BUILD_NUMBER}"
        }
       }
            stage('Deploy our image') {
                                        steps{
                                               script {
                                                            docker.withRegistry( 'https://registry.hub.docker.com', 'DockerHub' ) {
                                                            dockerImage.push()
                                                        }
                                                }            
                                               }
                                       }
    }
}

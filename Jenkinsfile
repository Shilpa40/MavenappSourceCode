  
pipeline {
    agent any   
    environment {
                    registry = "shilpabains/dock"
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
                            app= bat "docker build -t devops:${BUILD_NUMBER} ."
                        }
                      }
                   }
       stage("Cleaning Previous Deployment"){
            steps{
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                            bat "docker stop shilpa"
                            bat "docker rm -f shilpa"
                        }
            }
        }
       stage ("Docker Deployment")
        {
        steps
        {
        bat "docker run --name shilpa -d -p 8855:9000 devops:${BUILD_NUMBER}"
        }
       }
       
            stage('Deploy our image') {
                                        steps{
                                               script {
                                                            docker.withRegistry( 'https://registry.hub.docker.com', 'DockerHub' ) {
                                                            app.push()
                                                        }
                                                }            
                                        }     
                                  }                               
    }
}

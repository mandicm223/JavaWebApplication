pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }
    environment {
        ArtifactId = readMavenPom().getArtifactId()
        Version = readMavenPom().getVersion()
        Name = readMavenPom().getName()
        GroupId = readMavenPom().getGroupId()
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }

        // Stage3 : sending artifacts to Nexus
        stage ('Publish to Nexus'){
            steps {
                script(
                    
                    def NexusRepo = Version.endsWith("SNAPSHOT") ? "MarkoMandic-SNAPSHOT" : "MarkoMandic-RELEASE"


                    nexusArtifactUploader artifacts: 
                    [[artifactId: "${ArtifactId}", 
                    classifier: '', 
                    file: 'target/MarkoMandicLab-0.0.3-SNAPSHOT.war', 
                    type: 'war']], 
                    credentialsId: '6ee80c61-0baf-48b9-8975-91d6bba88303', 
                    groupId: "${GroupId}", 
                    nexusUrl: '172.20.10.112:8081', 
                    nexusVersion: 'nexus2', 
                    protocol: 'http', 
                    repository: "${NexusRepo}", 
                    version: "${Version}"
                )
            }
        }

        // Stage3 : Deploying
        stage ('Deploying'){
            steps {
                echo 'Deploying'
            }
        }

        
        
    }

}
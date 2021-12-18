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
                script{
                    
                def NexusRepo = Version.endsWith("SNAPSHOT") ? "MarkoMandic-SNAPSHOT" : "MarkoMandic-RELEASE"


                nexusArtifactUploader artifacts: 
                [[artifactId: "${ArtifactId}", 
                classifier: '', 
                file: "target/${ArtifactId}-${Version}.war", 
                type: 'war']], 
                credentialsId: 'd5af4919-b456-47dd-8a66-a97d932c56a5', 
                groupId: "${GroupId}", 
                nexusUrl: '172.20.10.21:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: "${NexusRepo}", 
                version: "${Version}"
                }
            }
        }
        
        // Stage4 : Deploying to apache
        stage ('Deploying to apache'){
            steps {
                echo 'Deploying...'
                sshPublisher(publishers:
                [sshPublisherDesc(
                    configName: 'Ansible_Controller',
                    transfers: [
                        sshTransfer(
                            cleanRemote: false,
                            execCommand: 'ansible-playbook /opt/playbooks/download_and_deploy_to_tomcat.yaml -i /opt/playbooks/hosts',
                            execTimeout: 120000
                        )
                    ],
                    usePromotionTimestamp: false,
                    useWorkspaceInPromotion: false,
                    verbose: false)
                    ])
            }
        }
        
        

        // Stage5 : Deploying to Docker 
        stage ('Deploying to docker'){
            steps {
                echo 'Deploying...'
                sshPublisher(publishers:
                [sshPublisherDesc(
                    configName: 'Ansible_Controller',
                    transfers: [
                        sshTransfer(
                            cleanRemote: false,
                            execCommand: 'ansible-playbook /opt/playbooks/deploy_to_docker.yaml -i /opt/playbooks/hosts',
                            execTimeout: 120000
                        )
                    ],
                    usePromotionTimestamp: false,
                    useWorkspaceInPromotion: false,
                    verbose: false)
                    ])
            }
        }    
        

        
        
    }

}
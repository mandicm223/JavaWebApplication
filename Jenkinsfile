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
                credentialsId: 'f4454ab9-b5cf-4a09-a5de-06f179844fc0', 
                groupId: "${GroupId}", 
                nexusUrl: '172.20.10.222:8081', 
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
                            execCommand: 'ansible-playbook /opt/playbooks/download_and_deploy_as_tomcat_user.yaml -i /opt/playbooks/hosts',
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
                            execCommand: 'ansible-playbook /opt/playbooks/download_and_deploy_docker.yaml -i /opt/playbooks/hosts',
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
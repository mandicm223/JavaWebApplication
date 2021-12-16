pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }

    stages {
        // Specify various stage within stages

        //stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }

        //stage 2. Testing
        stage ('Test') {
            steps {
                echo "Testing....."
            }
        }

        //stage 3. Deploying
        stage ('Deploying') {
            steps {
                echo "Deploying..."
            }
        }
        }

    }
}
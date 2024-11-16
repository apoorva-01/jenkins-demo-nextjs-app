pipeline {
    agent none  // No job scheduling on Jenkins master, everything happens on agents

    environment {
        ARTIFACTS_URL = 's3://your-bucket-name/artifacts/'
    }

    stages {
        stage('Find Available Agent and Allocate Task') {
            agent none  // No agent needed here, this is just orchestration logic
            steps {
                script {
                    def agentLabel = 'Slave-1-DigitalOcean-VPS' 

                    // Allocate the whole pipeline to this agent
                    node(agentLabel) {
                        // Run the rest of the pipeline on this agent
                        stage('Checkout Code') {
                            echo 'Cloning Next.js app repository...'
                            checkout scm
                        }

                        stage('Install Dependencies') {
                            try {
                                echo 'Installing dependencies...'
                                sh 'npm install'
                            } catch (Exception e) {
                                currentBuild.result = 'FAILURE'
                                throw e
                            }
                        }

                        stage('Build') {
                            try {
                                echo 'Building Next.js application...'
                                sh 'npm run build'
                            } catch (Exception e) {
                                currentBuild.result = 'FAILURE'
                                throw e
                            }
                        }

                        // stage('Store Artifacts') {
                        //     try {
                        //         sh "aws s3 cp ./build/output.zip ${ARTIFACTS_URL}/output-${BUILD_NUMBER}.zip"
                        //     } catch (Exception e) {
                        //         currentBuild.result = 'FAILURE'
                        //         throw e
                        //     }
                        // }
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully'
        }
        failure {
            echo 'Build failed'
        }
    }
}
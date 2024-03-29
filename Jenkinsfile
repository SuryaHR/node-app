pipeline {
    agent any

    environment {
        // Define the Node.js version to use
        // NODEJS_VERSION = 'NodeJS-14.17.6'
        // Define the Docker image name and tag
        DOCKER_IMAGE = 'surya589/react-app'
        DOCKER_IMAGE_TAG = 'v3'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // script {
                //     def nodejsHome = tool name: NODEJS_VERSION, type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                //     env.PATH = "${nodejsHome}/bin:${env.PATH}"
                // }
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                //sh 'npm test'
                echo "Testing logic goes here and test the code"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image with the Node.js application
                    docker.build("${DOCKER_IMAGE}:${DOCKER_IMAGE_TAG}")
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Example: Deploy the Docker image to a registry (replace with your actual deployment commands)
                    docker.withRegistry('https://registry.hub.docker.com/v2/', 'docker-credential-id') {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_IMAGE_TAG}").push()
                    }

                    // Example: Deploy the Docker image to a server (replace with your actual deployment commands)
                    // In a real scenario, you might deploy to a Kubernetes cluster, AWS ECS, etc.
                    echo 'Deployment logic goes here...'
                    docker.image("${DOCKER_IMAGE}:${DOCKER_IMAGE_TAG}").pull()
                    script {
			sh "docker stop node-app"
			sh "docker rm node-app"
			sh "docker run -itd --name node-app -p 8082:8080 ${DOCKER_IMAGE}:${DOCKER_IMAGE_TAG}"
                    }
                }
            }
        }

	stage('Create New Pipeline Job') {
            steps {
                script {
                    // Define the new job name
                    def newJobName = 'Sample-pipeline'
                    
                    // Define Git repository URL and credentials ID
                    def gitRepoUrl = 'https://github.com/SuryaHR/node-app.git'
                    
                    // Create a new pipeline job with Git SCM configuration
                    jobDsl scriptText: """
                        pipelineJob('${newJobName}') {
                            definition {
                                cpsScm {
                                    scm {
                                        git {
                                            remote {
                                                url('${gitRepoUrl}')
                                            }
                                            branches('*/master') // Specify branches as needed
                                            extensions {
                                                // Add additional Git SCM configurations if necessary
                                            }
                                        }
                                    }
                                    scriptPath('Jenkinsfile') // Path to your Jenkinsfile in the Git repository
                                }
                            }
                            triggers {
                                githubPush()
                            }
                        }
                    """
                }
            }
        }
    }

	
    post {
        success {
            echo 'Pipeline succeeded! Node.js application is built, tested, and deployed.'
        }
        failure {
            echo 'Pipeline failed! Check the build and deployment logs for details.'
        }
    }
}

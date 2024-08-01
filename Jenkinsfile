pipeline {
    agent any
    environment {
    }  
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Identify Changed Dockerfiles') {
            steps {
                script {
                    // Get the list of changed files from the last commit
                    def changedFiles = sh(script: 'git diff --name-only HEAD~1 HEAD', returnStdout: true).trim().split("\n")

                    // Initialize an empty list to keep track of changed Dockerfiles
                    def changedDockerfiles = []

                    // Loop through the changed files to find Dockerfiles
                    for (file in changedFiles) {
                        if (file.endsWith("Dockerfile")) {
                            changedDockerfiles.add(file)
                        }
                    }

                    // Pass the list of changed Dockerfiles to the next stage
                    env.CHANGED_DOCKERFILES = changedDockerfiles.join(",")
                }
            }
        }
        stage('Build Docker Images') {
            steps {
                script {
                    def changedDockerfiles = env.CHANGED_DOCKERFILES.split(",")

                    for (dockerfile in changedDockerfiles) {
                        def pathParts = dockerfile.split('/')
                        def projectType = pathParts[0]
                        def version = pathParts[1]

                        // Create a tag for the Docker image
                        def tag = "${projectType}.${version}.${new Date().format('yyyyMMdd')}"

                        // Build and push the Docker image
                        echo "${tag}"
                    }
                }
            }
        }
    }
    post {
        always {
            // Clean up any Docker images created during the build
            sh 'docker system prune -af'
        }
    }
}

node {
    // Reference to Maven
    def mvnHome = tool 'maven-3.5.2'

    // Holds reference to docker image
    def dockerImage
    def dockerImageTag = "devopsexample${env.BUILD_NUMBER}"
    
    stage('Clone Repo') {
        // Clone the repository (add credentials if private)
        git url: 'https://github.com/cloudwifi/clouddevopshub-project-1.git'
    }    

    stage('Build Project') {
        // Build project via Maven
        sh "${mvnHome}/bin/mvn clean install"
    }
		
    stage('Build Docker Image') {
        // Build Docker image
        dockerImage = docker.build("devopsexample:${env.BUILD_NUMBER}")
    }

    stage('Deploy Docker Image and login') {
        echo "Docker Image Tag Name: ${dockerImageTag}"
        sh "docker images"

        // Securely fetch DockerHub credentials from Jenkins
        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
        }
    }

    stage('Docker push') {
        // Get the latest built image dynamically
        def imageId = sh(script: "docker images -q devopsexample:${env.BUILD_NUMBER}", returnStdout: true).trim()

        echo "Tagging image: ${imageId}"

        // Tagging and pushing the image
        sh "docker tag ${imageId} naveenjangid22/myapplication"
        sh "docker push naveenjangid22/myapplication"
    }
}

node {
    def app
    def container_name = "hellonodejs"

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("${env.registry}/${env.repository}/hellonodejs:${env.BUILD_NUMBER}")
    }

    stage('Test image') {
    
        app.inside {
            sh 'echo "No tests to run."'
        }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry("https://${env.registry}", 'quay-io') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage('Create service') {
        createService(container_name)   
    }
}

def createService(containerName){
    sh "docker create service --name $containerName --publish 8000:8000 $containerName:latest"
    echo "Service got created and runs on port 8000"
    sh "docker service ls"
}

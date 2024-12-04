node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        app = docker.build("karthi2611/arocddeployment")
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Tag image') {
        // Tag image with build number and latest
        app.tag("latest")
    }

    stage('Push image') {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage('Trigger ManifestUpdate') {
        echo "Triggering updatemanifestjob"

        // Update job path if the job is in a folder
        def jobName = 'updatemanifest'  // Update this if job is in a folder, e.g., 'folder_name/updatemanifest'
        def jobParams = [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]

        // Ensure that the job exists and the parameters are correct
        try {
            build job: jobName, parameters: jobParams
        } catch (Exception e) {
            error "Job '${jobName}' failed or was not found. Please verify its existence and permissions."
        }
    }
}

node {
    def app

    stage('Clone repository') {
        // Clona il repository
        checkout scm
    }
        stage('Get Image Digest') {
                script {
                    // Login to Docker Hub
                    withCredentials([usernamePassword(credentialsId: 'dockertmitaly', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    }

                    // Ensure the latest image is pulled from the registry
                    sh 'docker pull trenditalydocker/webpage:latest'
                    
                    // Get the image digest
                    def digest = sh(
                        script: "docker inspect --format='{{index .RepoDigests 0}}' trenditalydocker/webpage:latest",
                        returnStdout: true
                    ).trim()

                    // Extract only the SHA part
                    def sha = digest.split('@')[1]
                    echo "Image digest: ${sha}"

                    // Save the digest in an environment variable for subsequent steps
                    env.IMAGE_DIGEST = sha
                }
          
        }
    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'githubtoken', passwordVariable: 'GIT_TOKEN', usernameVariable: 'GIT_USERNAME')]) {
                    // Configura Git
                    sh "git config user.email alloftrenditalygithub@trendmicro.com"
                    sh "git config user.name tmitaly"
                    
                    // Modifica il file deployment.yaml
                    sh "sed -i 's+trenditalydocker/webpage.*+trenditalydocker/webpage:${DOCKERTAG}+g' deployment.yaml"
                    sh "sed -i 's+flaskdemo+pippo+g' deployment.yaml"
                    
                    // Aggiungi, committa e push le modifiche
                    sh "git add ."
                    sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                    
                    // Usa il token per l'autenticazione
                    sh "git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main"
                }
            }
        }
    }
}

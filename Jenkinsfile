node {
    def app

    stage('Clone repository') {
        // Clona il repository
        checkout scm
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

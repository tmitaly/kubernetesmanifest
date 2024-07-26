node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Show Original Deployment YAML') {
        // Mostra il contenuto originale di deployment.yaml
        echo '--- Original deployment.yaml ---'
        sh "cat deployment.yaml"
    }

    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'githubtmitaly', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    // Configura Git
                    sh "git config user.email 'alloftrenditalygithub@trendmicro.com'"
                    sh "git config user.name 'tmitaly'"

                    // Leggi il digest dell'immagine dal parametro DOCKERTAG
                    def newDigest = env.DOCKERTAG

                    // Aggiorna il file deployment.yaml con il nuovo digest
                    sh "sed -i 's+trenditalydocker/webpage@sha256:[a-f0-9]\\{64\\}+trenditalydocker/webpage@sha256:${newDigest}+g' deployment.yaml"

                    // Mostra le modifiche apportate al file
                    echo '--- Updated deployment.yaml ---'
                    sh "cat deployment.yaml"

                    // Verifica se ci sono modifiche
                    def changes = sh(script: "git diff", returnStdout: true).trim()
                    if (changes) {
                        echo '--- Changes Detected ---'
                        echo "${changes}"
                        sh "git add deployment.yaml"
                        sh "git commit -m 'Update deployment.yaml with new image digest ${newDigest} by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main"
                    } else {
                        echo 'No changes detected in deployment.yaml'
                    }
                }
            }
        }
    }

    stage('Show Commit Diff') {
        // Mostra il diff del commit
        echo '--- Commit Diff ---'
        sh "git diff HEAD^ HEAD"
    }
}

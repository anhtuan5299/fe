pipeline {
    agent {
        label "anhtuan"
    }

    environment {
        GIT_COMMIT_SHORT = sh(
            script: "printf \$(git rev-parse --short ${GIT_COMMIT})",
            returnStdout: true
        )
        GIT_COMMIT_DATE = sh(
            script: "printf \$(git show -s --format=%ci ${GIT_COMMIT})",
            returnStdout: true
        )

        ARTIFACT_REPOSITORY = "anhtuan5299/fe:v2.0"
        CREDENTIAL_ID = "dockerhub"
        registryCredential = "dockerhub"

        BUILD_IMAGE = "fe-angular:${GIT_COMMIT_DATE.take(10)}_${GIT_COMMIT_SHORT}"
        DEPLOY_IMAGE = "${ARTIFACT_REPOSITORY}/${BUILD_IMAGE}"
    }

    stages {
        stage ("Validate") {
            steps {
                sh "docker version"
                sh "node --version"
                sh "npm version"
            }
        }

        stage ("Build docker image fe-angular") {
            steps {
                sh "docker build --pull --no-cache -f Dockerfile -t ${BUILD_IMAGE} ."
            }
        }

        stage ("Push docker image fe-angular to hub") {
            steps{
               script {
                   def appimage = docker.build ARTIFACT_REPOSITORY
                   docker.withRegistry( '', registryCredential ) {
                       appimage.push()
                   }
               }
           }

        }
    }

    post {
        always {
            echo 'Cleaning up workspace'
        }
    }
}

void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: env.GIT_URL],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                setBuildStatus("Building...", "PENDING");
                echo 'Building...'
                sh 'platformio run -e featheresp32'
            }
        }
        stage('Upload') {
            steps {
                setBuildStatus("Flashing firmware...", "PENDING");
                echo 'Flashing firmware....'
                sh 'platformio run -e featheresp32 -t upload'
            }
        }
        stage('Test') {
            steps {
                setBuildStatus("Testing...", "PENDING");
                echo 'Testing...'
                sh 'platformio test -e native'       // on the raspberry
                sh 'platformio test -e featheresp32' // on the devide
            }
        }
        stage('Deploy') {
            steps {
                setBuildStatus("Deploying...", "PENDING");
                echo 'Deploying....'
            }
        }
    }
    post {
        success {
            setBuildStatus("Build succeeded", "SUCCESS");
        }
        failure {
            setBuildStatus("Build failed", "FAILURE");
        }
    }
}
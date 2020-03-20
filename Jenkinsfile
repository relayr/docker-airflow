node('jenkins-neokami-slave', {

    def dockerTag
    airflowVersion = "1.10.9"

    withEnv(['AIRFLOW_VERSION=' + airflowVersion]) {
        stage('Checkout') {
            checkout scm
            git branch: "${env.BRANCH_NAME}"

            dockerTag = "${env.BRANCH_NAME}"
          }


        stage('Build and Publish Docker Image') {
            try {
                sh """
                    set -o pipefail
                    docker build -t relayr/docker-airflow:${AIRFLOW_VERSION} --build-arg AIRFLOW_VERSION=${AIRFLOW_VERSION} .
                    docker push relayr/docker-airflow:${AIRFLOW_VERSION}
                    docker rmi relayr/docker-airflow:${AIRFLOW_VERSION}
                """
            } catch (e) {
                notifyBuild('Failed', 'Build and Publish Docker Image')
                throw e
            }
        }
        stage('Push build informations to Version Tracking Service') {
          sh """#!/bin/bash -e
            export GITCOMMIT=\$(git rev-parse HEAD)
            curl --fail -X POST -H "Content-Type: application/json" -d "{\\"service\\":\\"relayr/docker-airflow\\",\\"docker_tag\\":\\"${dockerTag}\\",\\"git_branch\\":\\"${env.BRANCH_NAME}\\",\\"version\\":\\"git-commit \${GITCOMMIT}\\"}"  ${env.VTS_URL}/containers -v
          """
        }
    }
})

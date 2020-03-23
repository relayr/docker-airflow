node('jenkins-neokami-slave', {

    airflowVersion = "1.10.9"

    withEnv(['AIRFLOW_VERSION=' + airflowVersion]) {
        stage('Checkout') {
            checkout scm
          }

        properties([
                    buildDiscarder(
                            logRotator(
                                    artifactDaysToKeepStr: '',
                                    artifactNumToKeepStr: '20',
                                    daysToKeepStr: '',
                                    numToKeepStr: '20'
                            )
                    ),
                    pipelineTriggers([[$class: 'GitHubPushTrigger']])
            ])

        stage('Build Docker Image') {
            try {
                sh """
                    set -o pipefail
                    docker build -t relayr/docker-airflow:${AIRFLOW_VERSION} --build-arg AIRFLOW_VERSION=${AIRFLOW_VERSION} .
                """
            } catch (e) {
                notifyBuild('Failed', 'Build Docker Image')
                throw e
            }
        }

        if (env.BRANCH_NAME == "master") {
            stage('Publish Docker Image') {
                try {
                    sh """
                        set -o pipefail
                        docker push relayr/docker-airflow:${AIRFLOW_VERSION}
                    """
                } catch (e) {
                    notifyBuild('Failed', 'Publish Docker Image')
                    throw e
                }
            }
        } 

        stage('Delete Docker Image') {
            try {
                sh """
                    set -o pipefail
                    docker rmi relayr/docker-airflow:${AIRFLOW_VERSION}
                """
            } catch (e) {
                notifyBuild('Failed', 'Delete Docker Image')
                throw e
            }
        }
    }
})

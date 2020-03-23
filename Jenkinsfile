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

    if (env.BRANCH_NAME == "master") {
            stage('Publish and Publish Docker Image') {
                try {
                    sh """
                        set -o pipefail
                        docker build -t relayr/docker-airflow:${AIRFLOW_VERSION} --build-arg AIRFLOW_VERSION=${AIRFLOW_VERSION} .
                        docker push relayr/docker-airflow:${AIRFLOW_VERSION}
                        docker rmi relayr/docker-airflow:${AIRFLOW_VERSION}
                    """
                } catch (e) {
                    notifyBuild('Failed', 'Publish Docker Image')
                    throw e
                }
            }
        } else {
            try {
                sh """
                    set -o pipefail
                    docker build -t relayr/docker-airflow:${AIRFLOW_VERSION} --build-arg AIRFLOW_VERSION=${AIRFLOW_VERSION} .
                    docker rmi relayr/docker-airflow:${AIRFLOW_VERSION}
                """
            } catch (e) {
                notifyBuild('Failed', 'Build Docker Image')
                throw e
            }
        }
    }
})

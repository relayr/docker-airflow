node('jenkins-neokami-slave', {

    notifyBuild('Started', null)

    airflowVersion = "1.10.9"

    timestamps {
        withEnv(['AIRFLOW_VERSION=' + airflowVersion]) {

            stage('Preparing VirtualEnv') {
                cleanWs()
                checkout scm

            stage('Build and Publish Docker Image') {
                try {
                    sh """
                        docker build -t relayr/docker-airflow:${AIRFLOW_VERSION} --build-arg AIRFLOW_VERSION=${AIRFLOW_VERSION} .
                        docker push relayr/docker-airflow:${AIRFLOW_VERSION}
                        docker rmi relayr/docker-airflow:${AIRFLOW_VERSION}
                    """
                } catch (e) {
                    notifyBuild('Failed', 'Build and Publish Docker Image')
                    throw e
                }
            }
        }
    }
})



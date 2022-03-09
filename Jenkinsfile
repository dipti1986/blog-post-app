def IMAGE_NAME=''
pipeline {
    agent {
        label "main"
    }
    stages {
        stage('Build Docker Image') {
            steps {
                script{
                    IMAGE_NAME = "${JOB_NAME}".tokenize("/")[0]
                    sh "sudo docker build . --tag diptichoudhary/${IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }
        stage('Test App') {
            steps {
                script{
                    IMAGE_NAME = "${JOB_NAME}".tokenize("/")[0]
                    sh """
                        if [ -d "htmlcov" ]; then rm -Rf htmlcov; fi
                        mkdir htmlcov
                        sudo docker run -p 5000:5000 -v ${WORKSPACE}/htmlcov:/app/htmlcov diptichoudhary/${IMAGE_NAME}:${BUILD_NUMBER} pip install pytest pytest-cov && python3 -m pytest --cov=. --cov-report html
                    """
                }
                publishHTML target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: false,
                    keepAll: true,
                    reportDir: 'htmlcov',
                    reportFiles: 'index.html',
                    reportName: 'Code Coverage Report'
                ]
            }
        }
        stage('Docker Push Registry') {
            when { branch pattern: "master", comparator: "REGEXP"}
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                sh """
                    sudo docker login --username ${USERNAME} --password ${PASSWORD}
                    sudo docker push diptichoudhary/${IMAGE_NAME}:${BUILD_NUMBER}
                """
                }
            }
        }
        stage('Deploy To Docker Swarm') {
            agent {
                label "docker-swarm-manager"
            }
            when { branch pattern: "master", comparator: "REGEXP"}
            steps {
                sh """
                    sudo IMAGE_NAME=diptichoudhary/${IMAGE_NAME} IMAGE_TAG=${BUILD_NUMBER} docker stack deploy --compose-file ./docker-compose.yaml ${IMAGE_NAME}
                """
            }
        }
    }
}
pipeline {
    agent any
    environment {
        IMAGE_NAME = "ivanegorov22/django_deno"
        PROJECT_NAME = "common_django"
        DOMAIN = "egorov.prod.mshp-devops.com"
    }
    stages {
        stage("test") {
            steps {
                build job: '/lib/django-test-parametrized',
                    parameters: [
                        string(name: 'GIT_URL', value: "${GIT_URL}"),
                        string(name: 'GIT_BRANCH', value: "${GIT_BRANCH}")
                    ]
            }
        }
        stage("build") {
            steps {
                build job: '/lib/django build parametrized',
                    parameters: [
                        string(name: 'GIT_URL', value: "${GIT_URL}"),
                        string(name: 'GIT_BRANCH', value: "${GIT_BRANCH}"),
                        string(name: 'IMAGE_NAME', value: "${IMAGE_NAME}"),
                        string(name: 'GIT_COMMIT_HASH', value: "${GIT_COMMIT}")
                    ]
            }
        }
        stage("push") {
            steps {
                withCredentials(
                    [
                        usernamePassword(usernameVariable: 'LOGIN', passwordVariable: 'PASSWORD', credentialsId: 'Ivan_docker_hub')
                        ]
                    ) {
                        sh 'docker login -u ${LOGIN} -p ${PASSWORD}'
                        sh 'docker push ${IMAGE_NAME}:latest'
                        sh 'docker push ${IMAGE_NAME}:${GIT_COMMIT}'
                    }
            }
        }
        stage('deploy') {
            steps {
                withCredentials(
                    [
                        sshUserPrivateKey(credentialsId: "devops_prod_key", keyFileVariable: 'KEY_FILE', usernameVariable:'USERNAME'),
                        string(credentialsId: "devops_prod_address", variable: 'SERVER_ADDRESS')
                    ]
                ) {
                    sh 'ssh -o StrictHostKeyChecking=no -i "${KEY_FILE}" ${USERNAME}@${SERVER_ADDRESS} mkdir -p ${PROJECT_NAME}'
                    sh 'scp -o StrictHostKeyChecking=no -i "${KEY_FILE}" docker-compose.yaml ${USERNAME}@${SERVER_ADDRESS}:${PROJECT_NAME}/'
                    sh 'ssh -o StrictHostKeyChecking=no -i "${KEY_FILE}" ${USERNAME}@${SERVER_ADDRESS} docker compose -f ${PROJECT_NAME}/docker-compose.yaml pull'
                    sh 'ssh -o StrictHostKeyChecking=no -i "${KEY_FILE}" ${USERNAME}@${SERVER_ADDRESS} docker compose -f ${PROJECT_NAME}/docker-compose.yaml up -d'
                    sh 'scp -o StrictHostKeyChecking=no -i "${KEY_FILE}" egorov.prod.mshp-devops.com.conf ${USERNAME}@${SERVER_ADDRESS}:nginx'
                    sh 'ssh -o StrictHostKeyChecking=no -i "${KEY_FILE}" ${USERNAME}@${SERVER_ADDRESS} sudo certbot --nginx -d ${DOMAIN} --non-interactive --agree-tos -m test@test.com'
                    sh 'ssh -o StrictHostKeyChecking=no -i "${KEY_FILE}" ${USERNAME}@${SERVER_ADDRESS} sudo systemctl reload nginx'
                }
            }
        }

    }
}

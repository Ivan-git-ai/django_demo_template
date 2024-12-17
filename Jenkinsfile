pipeline {
    agent any
    environment {
        IMAGE_NAME = 'Ivan-git-ai/django_demo_template'
        GIT_COMMIT = 'c6918b04713325c1428dc2a7536b14569d7b2b2f' 
    }
    stages {
        stage('Run Tests Pipeline') {
            steps {
                build job: 'lib/django-test-parametrized',
                      parameters: [
                          string(name: 'GIT_URL', value: 'https://github.com/Ivan-git-ai/django_demo_template.git'),
                          string(name: 'GIT_BRANCH', value: 'main'),
                          string(name: 'GIT_COMMIT', value: '')
                      ]
            }
        }
        stage('Run Build Pipeline') {
            steps {
                build job: 'lib/django-build-parametrized',
                      parameters: [
                          string(name: 'GIT_URL', value: 'https://github.com/Ivan-git-ai/django_demo_template.git'),
                          string(name: 'GIT_BRANCH', value: 'main'),
                          string(name: 'GIT_COMMIT_HASH', value: GIT_COMMIT),
                          string(name: 'IMAGE_NAME', value: IMAGE_NAME)
                      ]
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "Ivan_docker_hub", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        '''
                        sh "docker push ${IMAGE_NAME}:latest"
                        sh "docker push ${IMAGE_NAME}:${GIT_COMMIT}"
                    }
                }
            }
        }
    }
}

pipeline {
    agent any
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
                build job: 'lib/django build parametrized',
                      parameters: [
                          string(name: 'GIT_URL', value: 'https://github.com/Ivan-git-ai/django_demo_template.git'),
                          string(name: 'GIT_BRANCH', value: 'main'),
                          string(name: 'GIT_COMMIT_HASH', value: '92bad5c42bb69b1e0e344fd25c5a7945a09dc399'),
                          string(name: 'IMAGE_NAME', value: 'Ivan-git-ai/django_demo_template')
                      ]
            }
        }
    }
}

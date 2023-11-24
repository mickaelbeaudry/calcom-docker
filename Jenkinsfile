pipeline {
    agent any

    stages {
        stage('Preparing') {
            steps {
                sh 'git submodule update --init --recursive'
                echo 'Preparing database to build image'
                sh 'yes | docker system prune -a'
                sh 'yes | docker volume prune -a'
                sh 'docker compose up -d'
                sh 'sleep 180'
                sh 'docker compose down'
                sh 'docker compose up -d database'

            }
        }
        stage('Compiling image') {
            steps {
                sh '''DOCKER_BUILDKIT=0 docker compose build calcom'''
            }
        }

        stage('Preparing yml compose file') {
            steps {
                sh 'docker compose -f docker-compose-swarm.yaml config > docker-compose-generated.yml'
                sh 'sed -i \'1d\'  docker-compose-generated.yml'
                sh 'echo -e "version: \'3.8\'\n$(cat docker-compose-generated.yml)" > docker-compose-generated.yml'
            }
        }


    }

    post {
        always {
            echo 'One way or another, I have finished'
            sh 'docker compose down'
        }
        success {
            echo 'I succeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Preparing') {
            steps {
                sh 'git submodule update --init --recursive'
                echo 'Preparing database to build image'
            }
        }
        stage('Compiling image') {
            steps {
                sh '''docker compose up -d database && docker build -t 10.10.17.161:5000/aktioalta-calendso:v2.7.8 \
                    --build-arg NEXT_PUBLIC_WEBAPP_URL='https://rendez-vous.aktioalta.com' \
                    --build-arg NEXT_PUBLIC_LICENSE_CONSENT='agree' \
                    --build-arg CALCOM_TELEMETRY_DISABLED='1' \
                    --build-arg NEXTAUTH_SECRET='mZAcmyuGf2kLjn2OPOhKQsoK0nLwLR8AsTSW3gRX8' \
                    --build-arg CALENDSO_ENCRYPTION_KEY='3MPc1LVtVKobejtnSv4d5fta8EJYSfLa' \
                    --build-arg DATABASE_URL='postgresql://unicorn_user_123:magical_password_321@database:5432/calendso_db' \
                    --build-arg BASE_URL='https://rendez-vous.aktioalta.com' .'''
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

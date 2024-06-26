pipeline {
    agent any
    environment {
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Pull and Run Containers') {
            steps {
                script {
                    // Pull the necessary Docker images
                    sh 'docker pull postgres:latest'
                    sh 'docker pull redis:latest'
                    sh 'docker pull mongo:latest'
                    sh 'docker pull mongo-express:latest'

                    // Tag and push the images to the local Docker registry
                    sh 'docker tag postgres:latest sameerahmedbalaganur/postgres:latest'
                    sh 'docker tag redis:latest sameerahmedbalaganur/redis:latest'
                    sh 'docker tag mongo:latest sameerahmedbalaganur/mongo:latest'
                    sh 'docker tag mongo-express:latest sameerahmedbalaganur/mongo-express:latest'

                    sh 'docker push sameerahmedbalaganur/postgres:latest'
                    sh 'docker push sameerahmedbalaganur/redis:latest'
                    sh 'docker push sameerahmedbalaganur/mongo:latest'
                    sh 'docker push sameerahmedbalaganur/mongo-express:latest'

                    // Run the containers locally
                    sh 'docker run -d --name postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 sameerahmedbalaganur/postgres:latest'
                    sh 'docker run -d --name redis -p 6379:6379 sameerahmedbalaganur/redis:latest'
                    sh 'docker run -d --name mongo -e MONGO_INITDB_ROOT_USERNAME=ati -e MONGO_INITDB_ROOT_PASSWORD=ati1234 -p 27017:27017 sameerahmedbalaganur/mongo:latest'
                    sh 'docker run -d --name mongo-express -e ME_CONFIG_MONGODB_ADMINUSERNAME=ati -e ME_CONFIG_MONGODB_ADMINPASSWORD=ati1234 -e ME_CONFIG_MONGODB_SERVER=mongo -p 8081:8081 --link mongo:mongo sameerahmedbalaganur/mongo-express:latest'
                }
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}

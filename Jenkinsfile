pipeline {
	agent any
	
    stages {
        stage('ARM build') {
            agent { label 'arm' }

            steps {
                sh 'docker build --pull -t registry.reactiveraven.co.uk/macrowatch:${BUILD_ID}-arm -f Dockerfile .'
                sh 'docker push registry.reactiveraven.co.uk/macrowatch:${BUILD_ID}-arm'
                sh 'docker tag registry.reactiveraven.co.uk/macrowatch:${BUILD_ID}-arm registry.reactiveraven.co.uk/macrowatch:latest-arm'
                sh 'docker push registry.reactiveraven.co.uk/macrowatch:latest-arm'
            }
        }

        stage('amd64 build') {
            agent { label 'amd64' }

            steps {
                script {
                    def built = docker.build("registry.reactiveraven.co.uk/macrowatch:${env.BUILD_ID}-amd64")

                    docker.withRegistry('http://registry.reactiveraven.co.uk', 'registry.reactiveraven.co.uk') {
                        built.push()
                        built.push('latest-amd64')
                    }
                }
            }
        }

        stage('manifest') {
            agent { label 'amd64' }

            steps {
                script {
                    docker.withRegistry('http://registry.reactiveraven.co.uk', 'registry.reactiveraven.co.uk') {
                        sh 'docker manifest create registry.reactiveraven.co.uk/macrowatch:${BUILD_ID} --amend registry.reactiveraven.co.uk/macrowatch:${BUILD_ID}-arm --amend registry.reactiveraven.co.uk/macrowatch:${BUILD_ID}-amd64'
                        sh 'docker manifest push registry.reactiveraven.co.uk/macrowatch:${BUILD_ID}'
                        sh 'docker manifest create registry.reactiveraven.co.uk/macrowatch:latest --amend registry.reactiveraven.co.uk/macrowatch:latest-arm --amend registry.reactiveraven.co.uk/macrowatch:latest-amd64'
                        sh 'docker manifest push registry.reactiveraven.co.uk/macrowatch:latest'
                    }
                }
            }
        }
    }
}

pipeline {
    agent {
        docker { image 'ubuntu:latest' }
    }
    stages {
        stage('Clone Repo') {
            steps {
                sh """
                    echo "Cloning the repository"
                    git clone https://github.com/JawaidAkhtar/Netflix-clone.git
                    cd Netflix-clone
                    echo "Cloned the repository successfully"
                """
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    def commitId = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                    sh 'docker build --build-arg TMDB_V3_API_KEY=${API_Key} -t ${DockerHub_User}/netflix-clone:${commitId} .'
                    env.COMMIT_ID = commitId
                    sh 'echo "docker image built with commit id ${commitId}"'
                }
            }
        }

        stage('Docker Run') {
            steps {
                sh """
                    echo "Trying to run the docker container"
                    docker run -d -p 80:80 --name netflix-clone ${DockerHub_User}/netflix-clone:${COMMIT_ID}
                    echo "Docker container is running"
                """
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DockerHubCred', usernameVariable: 'DockerHub_User', passwordVariable: 'DockerHub_Password')]) {
                    sh 'docker login -u ${DockerHub_User} -p ${DockerHub_Password}'
                    sh 'docker push ${DockerHub_User}/netflix-clone:${COMMIT_ID}'
                }
            }
        }

        stage('update Helm Chart') {
            steps {
                sh 'sed -i \'s|tag: .*|tag: ${COMMIT_ID}|\' ./helm/netflix-clone-chart/values.yaml'
            }
        }

        stage('Push Changes To GitHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHubCred', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_ACCESS_TOKEN')]) {
                    sh """
                        git add values.yaml
                        git commit -m "Updated tag in values.yaml to ${COMMIT_ID}"
                        git push https://${GITHUB_USERNAME}:${GITHUB_ACCESS_TOKEN}@github.com/JawaidAkhtar/Netflix-clone.git HEAD:main
                    """
                }
            }
        }
    }
}

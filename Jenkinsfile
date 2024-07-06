pipeline {
    agent any

    environment {
        registry = "docker-registry.default.svc:5000/youtube-clone-ns/youtube-clone"
        registryCredential = 'openshift-docker-credentials'
        dockerImage = ''
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/mahmoudhossni147852/Youtube-Clone'
            }
        }
        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deploy to OpenShift') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject('youtube-clone-ns') {
                            openshift.newApp(dockerImage.imageName())
                            openshift.selector('svc', 'youtube-clone').expose()
                        }
                    }
                }
            }
        }
    }
}

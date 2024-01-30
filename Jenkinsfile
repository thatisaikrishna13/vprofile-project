pipeline {
    agent any

    environment {
        registryCredentials = credentials('ecr-deploy') 
        appRegistry = '177290857852.dkr.ecr.eu-south-2.amazonaws.com/keduapp'
        keduRegistry = 'https://177290857852.dkr.ecr.eu-south-2.amazonaws.com/keduapp'
    }

    stages {
        stage('Build Kedu Image') {
            steps {
                script {
                    dockerImage = docker.build("${appRegistry}:${BUILD_NUMBER}", ".")
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    docker.withRegistry(keduRegistry, 'ecr:eu-south-2:ecr-deploy') {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Authenticate Docker Client') {
            steps {
                script {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'ecr-deploy', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                    sh "aws ecr get-login-password --region eu-south-2 | docker login --username AWS --password-stdin 177290857852.dkr.ecr.eu-south-2.amazonaws.com"
                    }
                }
            }
        }

        stage('Deploy to Prod') {
            steps {
                script {
                    sh "docker pull ${appRegistry}:${BUILD_NUMBER}"
                    sh "cd /mnt/kedu/kedu && docker compose up -d"
                }
            }
        }

        stage('Remove Old Docker Images') {
            steps {
                script {
                    sh "docker images --format '{{.Repository}}:{{.Tag}}' | grep -v -E '${appRegistry}:latest|postgres:15-alpine|ghcr.io/tecnativa/docker-duplicity-docker:latest' | xargs -I {} docker rmi {} || true"
                }
            }
        }
    }
}



pipeline {
    agent any

    environment {
        registryCredentials = credentials('ecr-deploy') 
        appRegistry = '177290857852.dkr.ecr.eu-south-2.amazonaws.com/keduapp'
        keduRegistry = 'https://177290857852.dkr.ecr.eu-south-2.amazonaws.com/keduapp'
        repoName = 'keduapp'
    }

    stages {
        stage('Build Kedu Image') {
            steps {
                script {
                    dockerImage = docker.build("${appRegistry}:${BUILD_NUMBER}", ".")
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    docker.withRegistry(keduRegistry, 'ecr:eu-south-2:ecr-deploy') {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Authenticate Docker Client') {
            steps {
                script {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'ecr-deploy', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                    sh "aws ecr get-login-password --region eu-south-2 | docker login --username AWS --password-stdin 177290857852.dkr.ecr.eu-south-2.amazonaws.com"
                    }
                }
            }
        }

        stage('Deploy to Prod') {
            steps {
                script {
                    sh "cd /mnt/kedu/kedu"
                    sh "docker compose down"
                    sh "docker rmi $appRegistry:latest"
                    sh "docker compose up -d"
                }
            }
        }
    }
}
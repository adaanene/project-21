pipeline{
    agent any

    environment {
        TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
        max = 20
        random_num = "${Math.abs(new Random().nextInt(max+1))}"
    }

    stages{
        stage("Workspace Cleanup") {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout Git') {
            steps {
                git branch: 'main', credentialsId: 'git', url: 'https://github.com/adaanene/project-20.git'
            }
        }

        stage('Building image') {
            environment {
                DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
            }
            
            steps {
                script {
                
                    sh " docker login -u adaane -p ${env.DOCKER_HUB_CREDENTIALS}"
                    sh " docker build -t adaane/todo-prj20:${env.TAG} ."
                }
            }
        }

        stage('Creating docker container') {
            steps {
                script {
                    sh " docker run -d --name todo-app-${env.random_num} -p 8000:8000 adaane/todo-prj20:${env.TAG}"
                }
            }
        }

        stage("Smoke Test") {
            steps {
                script {
                    sh "sleep 60"
                    sh "curl -I 8000:8000"
                }
            }
        }

        stage("Publish to Registry") {
            steps {
                script {
                    sh " docker push adaane/todo-prj20:${env.TAG}"
                }
            }
        }

        stage ('Clean Up') {
            steps {
                script {
                    sh " docker stop todo-app-${env.random_num}"
                    sh " docker rm todo-app-${env.random_num}"
                    sh " docker rmi adaane/todo-prj20:${env.TAG}"
                }
            }
        }

        stage ('logout Docker') {
            steps {
                script {
                    sh " docker logout"
                }
            }
        }
    }
   
}

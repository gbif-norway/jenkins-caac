pipeline {
    agent {
        kubernetes {
            yaml '''
                apiVersion: v1
                kind: Pod
                spec:
                  containers:
                  - name: kaniko
                    image: gcr.io/kaniko-project/executor:debug
                    command:
                    - /busybox/sleep
                    args:
                    - 99d
                    volumeMounts:
                      - name: docker-config
                        mountPath: /kaniko/.docker
                  volumes:
                    - name: docker-config
                      secret:
                        secretName: jenkins-docker-config
            '''
        }
    }
    
    parameters {
        string(name: 'DOCKER_REGISTRY', defaultValue: 'ghcr.io', description: 'Container registry URL')
        string(name: 'GITHUB_WEBHOOK_SECRET', defaultValue: '', description: 'GitHub webhook secret')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build and Push Container') {
            steps {
                container(name: 'kaniko') {
                    withCredentials([usernamePassword(credentialsId: 'docker-registry-credentials', 
                                                    usernameVariable: 'DOCKER_USERNAME', 
                                                    passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                            /kaniko/executor \
                            --context "${WORKSPACE}" \
                            --dockerfile "${WORKSPACE}/Dockerfile" \
                            --destination "${DOCKER_REGISTRY}/${JOB_NAME}:${BUILD_NUMBER}" \
                            --destination "${DOCKER_REGISTRY}/${JOB_NAME}:latest"
                        '''
                    }
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
} 
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "vikimano/trend-app"
        IMAGE_TAG    = "${BUILD_NUMBER}"
        KUBECONFIG   = "/var/lib/jenkins/.kube/config"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/Viki07-14/Trendapp.git'
            }
        }

        stage('Build Image using Docker Compose') {
            steps {
                sh '''
                  docker compose -f docker/docker-compose.yml build --no-cache
                  docker tag docker-trend-app:latest $DOCKER_IMAGE:latest
                  docker tag docker-trend-app:latest $DOCKER_IMAGE:$IMAGE_TAG
                  docker images | grep trend
                '''
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker push $DOCKER_IMAGE:latest
                      docker push $DOCKER_IMAGE:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Run App using Docker Compose') {
            steps {
                sh '''
                  docker compose -f docker/docker-compose.yml down --remove-orphans
                  docker compose -f docker/docker-compose.yml up -d
                '''
            }
        }

        stage('Verify EKS Access') {
            steps {
                sh '''
                  echo "Using kubeconfig: $KUBECONFIG"
                  kubectl config current-context
                  kubectl get nodes
                '''
            }
        }

        stage('Kubernetes EKS Deploy') {
            steps {
                sh '''
                  kubectl apply -f k8s/
                  kubectl rollout status deployment/trend-app
                  kubectl get pods
                  kubectl get svc
                '''
            }
        }
        
        stage('Get LoadBalancer Hostname') {
            steps {
                sh '''
                  # Get the LoadBalancer hostname
                  LB_HOSTNAME=$(kubectl get svc trend-app-service \
                    -n default \
                    -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

                  echo "🌐 LoadBalancer Hostname: $LB_HOSTNAME"
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment successful 🚀"
        }
        failure {
            echo "Deployment failed ❌"
        }
    }
}

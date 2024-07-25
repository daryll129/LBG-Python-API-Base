pipeline {
    agent any
    environment {
        GCR_CREDENTIALS_ID = 'projectteam2' // The ID you provided in Jenkins credentials1
        IMAGE_NAME = 'lbgpy'
        GCR_URL = 'europe-west1-docker.pkg.dev/lbg-mea-20/zibs-docker-repo'
        PROJECT_ID = 'lbg-mea-20'
        CLUSTER_NAME = 't2-proj3-cluster'
        LOCATION = 'europe-west1-d'
        CREDENTIALS_ID = 'team2kube'
    }
    stages {
        stage('Build and Push to GCR') {
            steps {
                script {
                    // Authenticate with Google Cloud
                    withCredentials([file(credentialsId: GCR_CREDENTIALS_ID, variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                        sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
                    }
                // Configure Docker to use gcloud as a credential helper
                sh 'gcloud auth configure-docker --quiet'
        sh 'gcloud auth configure-docker europe-west1-docker.pkg.dev'
                // Build the Docker image
                sh "docker build -t ${GCR_URL}/${IMAGE_NAME}:${BUILD_NUMBER} ."
                // Push the Docker image to GCR
                sh "docker push ${GCR_URL}/${IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }
        stage('Deploy to GKE') {
            steps {
                script {
                    // Deploy to GKE using Jenkins Kubernetes Engine Plugin
                   step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'kubernetes/deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
                }
            }
        }
    }
}

pipeline {
    agent any

	parameters {
        string(name: 'IMAGE_TAG', description: 'Docker image tag to deploy')
    }

    environment {
        HELM_BIN = "/usr/local/bin/helm"
        APP_NAME = "petclinic-app"
        CHART_NAME = "petclinic"
        NAMESPACE = "petclinic-ns"
        IMAGE_REPO = "registry.hub.docker.com/azureadmin12/petclinic"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Checking out source code"
                checkout scm
            }
        }

        stage('Prepare Helm Chart') {
            steps {
                echo "Preparing Helm chart"
                sh '''
                  pwd
                  cp -R helm/* .
                  ls -ltr
                '''
            }
        }

        stage('Deploy on Kubernetes using Helm') {
            steps {
                echo "Deploying application to Kubernetes"
                sh """
                ${HELM_BIN} upgrade --install ${APP_NAME} ${CHART_NAME} \
                  --namespace ${NAMESPACE} \
                  --create-namespace \
                  --set image.repository=${IMAGE_REPO} \
                  --set image.tag=${IMAGE_TAG}
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                echo "Verifying deployment"
                sh """
                helm list -n ${NAMESPACE}
                """
				// kubectl get pods -n ${NAMESPACE} // k8s host in different ec2
                // kubectl get svc -n ${NAMESPACE}
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful"
        }
        failure {
            echo "❌ Deployment failed"
        }
    }
}

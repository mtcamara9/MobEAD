pipeline {
    agent any
    environment {
        APP_NAME = "mobead"
        DEV_NAMESPACE = "mobead-dev"
        PROD_NAMESPACE = "mobead-prd"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('Preparar ImageStream') {
            steps {
                script {
                    sh "oc get imagestream ${APP_NAME} --namespace=${DEV_NAMESPACE} || oc create imagestream ${APP_NAME} --namespace=${DEV_NAMESPACE}"
                    sh "oc get imagestream ${APP_NAME} --namespace=${PROD_NAMESPACE} || oc create imagestream ${APP_NAME} --namespace=${PROD_NAMESPACE}"
                }
            }
        }
        stage('Iniciar Build em Dev') {
            steps {
                script {
                    sh "oc start-build ${APP_NAME} --namespace=${DEV_NAMESPACE} --follow"
                }
            }
        }
        stage('Deploy em Dev') {
            steps {
                script {
                    sh "oc tag ${DEV_NAMESPACE}/${APP_NAME}:${IMAGE_TAG} ${DEV_NAMESPACE}/${APP_NAME}:latest"
                    sh "oc rollout restart deployment/${APP_NAME} -n ${DEV_NAMESPACE}"
                }
            }
        }
        stage('Aprovação para Produção') {
            steps {
                input message: 'Aprovar Deploy em Produção?', ok: 'Deploy'
            }
        }
        stage('Iniciar Build em Produção') {
            steps {
                script {
                    sh "oc start-build ${APP_NAME} --namespace=${PROD_NAMESPACE} --follow"
                }
            }
        }
        stage('Deploy em Produção') {
            steps {
                script {
                    sh "oc tag ${PROD_NAMESPACE}/${APP_NAME}:${IMAGE_TAG} ${PROD_NAMESPACE}/${APP_NAME}:latest"
                    sh "oc rollout restart deployment/${APP_NAME} -n ${PROD_NAMESPACE}"
                }
            }
        }
    }
}

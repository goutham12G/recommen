
pipeline {

  environment {
    PROJECT = "useful-cathode-334010"
    APP_NAME = "adservice"
    FE_SVC_NAME = "${APP_NAME}-frontend"
    CLUSTER = "kubernet"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      inheritFrom 'sample-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
 # serviceAccountName: cd-jenkins
  containers:
  - name: python-bld
    image: python:buster
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/google.com/cloudsdktool/cloud-sdk
    command:
    - cat
    tty: true
  - name: kubectl
    image: google/cloud-sdk
    command:
    - cat
    tty: true
"""
}
  }
  stages {
    stage('codebuild') {
      steps {
        container('python-bld') {
          sh """
             ls -a && pwd 
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} ."
        }
      }
    } 
    stage('Deploy Dev') {
      steps {
        container('kubectl') {
          sh "gcloud container clusters get-credentials kubernet --zone us-central1-c --project useful-cathode-334010"
          sh "kubectl apply -f recommendationservice.yaml"
         
        }
      }
    }
  }
}

pipeline {
  options {
    timeout (time: 35, unit:"MINUTES")
  }
  agent {
    kubernetes {
      label "my-angular-slave"
      defaultContainer "jnlp"
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    component: ci
spec:
  serviceAccount: default
  volumes:
  - name: dockersock
    hostPath:
      path: "/var/run/docker.sock"
  - name: docker
    hostPath:
      path: "/usr/bin/docker"
  - name: google-cloud-key
    secret:
      secretName: registry-jenkins
  containers:
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    volumeMounts:
    - name: google-cloud-key
      readOnly: true
      mountPath: "/var/secrets/google"
    - name: docker
      mountPath: "/usr/bin/docker"
    - name: dockersock
      mountPath: "/var/run/docker.sock"
    command:
    - cat
    env:
    - name: GOOGLE_APPLICATION_CREDENTIALS
      value: /var/secrets/google/key.json
    tty: true
  - name: node
    image: node:lts-alpine
    env:
    - name: NO_PROXY
      value: "localhost, 0.0.0.0/4201, 0.0.0.0/9876"
    - name: CHROME_BIN
      value: /usr/bin/chromium-browser
    command:
    - cat
    tty: true
  - name: docker
    image: docker:19.03
    volumeMounts:
    - name: google-cloud-key
      readOnly: true
      mountPath: "/var/secrets/google"
    - name: docker
      mountPath: "/usr/bin/docker"
    - name: dockersock
      mountPath: "/var/run/docker.sock"
    command:
    - cat
    env:
    - name: GOOGLE_APPLICATION_CREDENTIALS
      value: /var/secrets/google/key.json
    tty: true
"""
    }
  }
  environment {
    COMMITTER_EMAIL = sh (
      returnStdout: true,
      script: "git --no-pager show -s --format=\'%ae\'"
    ).trim()
    TAG_NAME = sh (
      returnStdout: true,
      script: 'git tag --points-at HEAD | awk NF'
    ).trim()
  }

  stages {
    stage("Initialize") {
      steps {
        container('gcloud') {
          slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
          sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
          sh "gcloud config set project ${PROJECT_ID}"
        }
        container('docker') {
          sh "apk update"
          sh "apk add curl"
          sh "curl -fsSL https://github.com/GoogleCloudPlatform/docker-credential-gcr/releases/download/v2.0.0/docker-credential-gcr_linux_amd64-2.0.0.tar.gz | tar xz --to-stdout ./docker-credential-gcr > /usr/bin/docker-credential-gcr && chmod +x /usr/bin/docker-credential-gcr"
          sh "docker-credential-gcr configure-docker"
          sh 'docker --version'
        }

        
        
      }
    }
  }  
}  

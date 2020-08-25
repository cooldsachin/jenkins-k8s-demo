pipeline {
  agent {
    kubernetes {
      //label "angular-slave"
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
  stages {
    stage("Initialize") {
      steps {
        sh "echo my job"
      }
    }
  }  
}  

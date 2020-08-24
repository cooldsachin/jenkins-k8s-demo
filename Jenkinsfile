pipeline {
  agent {
    kubernetes {
      label 'parent-pod'
      yaml """
spec:
  containers:
  - name: golang
    image: golang:1.6.3-alpine
    command:
    - cat
    tty: true
"""
    }
  }
  stages {
    stage('Run maven') {
        agent {
            kubernetes {
                label 'nested-pod'
                yaml """
spec:
  containers:
  - name: maven
    image: maven:3.3.9-jdk-8-alpine
    command:
    - cat
    tty: true
"""
            }
        }
      
    }
  }
}

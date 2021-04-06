pipeline {
  agent {
    kubernetes {
      label 'example-kaniko-volumes'
      yaml """
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: jnlp
    workingDir: /tmp/jenkins
  - name: kaniko
    workingDir: /tmp/jenkins
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: docker-credentials
          items:
            - key: .dockerconfigjson
              path: config.json
"""
    }
  }
  stages {
    stage("Checkout code") {
      steps {
        checkout scm
      }
    }
    stage('Build with Kaniko') {
      environment {
        PATH = "/busybox:/kaniko:$PATH"
      }
      steps {
        container(name: 'kaniko', shell: '/busybox/sh') {

          writeFile file: "Dockerfile", text: """
            FROM nginx:latest
            COPY index.html /usr/share/nginx/html/index.html2
          """
          
          sh '''#!/busybox/sh
            /kaniko/executor --context `pwd` --verbosity debug --destination gtacloud/gf-demo:$GIT_COMMIT
          '''
        }
      }
    }
  }
}

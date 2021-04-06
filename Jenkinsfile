def label = "mypod-${UUID.randomUUID().toString()}"
podTemplate(label: label, yaml: """
kind: Pod
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    securityContext:
        privileged: true
        runAsUser: 0
    command: ["/bin/sh","-c"]
    args: ["sysctl -w vm.max_map_count=262144 && cat"]
    tty: true
    envFrom:
    - configMapRef:
        name: gfenv
    volumeMounts:
    - name: gf
      mountPath: /gf
  - name: mysql
    image: mysql:8
    args: ["--default-authentication-plugin=mysql_native_password"]
    envFrom:
    - configMapRef:
        name: gfenv
    volumeMounts:
    - name: gf
      mountPath: /docker-entrypoint-initdb.d
      subPath: dev-ops/db-container/sql/mysql-init
  - name: gf 
    image: gtagroup/public-projects:gf-full
    command: ["sleep","infinity"]
    envFrom:
    - configMapRef:
        name: gfenv
  volumes:
  - name: gf
    persistentVolumeClaim:
      claimName: gf  
"""
  ) {

  node(label) {
    stage('Test env') {
      container('ubuntu') {
        sh 'cp -r /gf . && pwd && ls'
      }
    }
    stage ('copy node modules') {
      container('gf') {
        sh 'cp -r /opt/*.tar.gz /app'
      }
    }
    stage ('extract frontend') {
      container('gf') {
        sh 'cd /app && tar -xzf front-end.tar.gz'
      }
    }
    stage ('extract backend') {
      container('gf') {
        sh 'cd /app && tar -xzf back-end.tar.gz'
      }
    }
    stage ('extract testing') {
      container('gf') {
        sh 'cd /app && tar -xzf testing.tar.gz'
      }
    }
    stage('Verify artefacts')
      container('gf') {
        sh 'ls /app && ls /app/front-end/node_modules'
      }    
    stage('verify sql dump files') {
      container('mysql') {
        sh 'ls /docker-entrypoint-initdb.d'
      }
    }
  }
}
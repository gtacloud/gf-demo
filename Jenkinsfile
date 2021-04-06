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
    volumeMounts:
    - name: gf
      mountPath: /app
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
    stage('Verify artefacts')
      container('gf') {
        sh 'ls /app'
      }
    stage('verify sql dump files') {
      container('mysql') {
        sh 'ls /docker-entrypoint-initdb.d'
      }
    }
  }
}
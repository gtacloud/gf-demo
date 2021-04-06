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
  - name: mysql
    image: mysql:8
    args: ["--default-authentication-plugin=mysql_native_password"]
    envFrom:
    - configMapRef:
        name: gfenv
  - name: gf 
    image: gtagroup/public-projects:gf-full
    envFrom:
    - configMapRef:
        name: gfenv    
"""
  ) {

  node(label) {
    stage('Test env') {
      container('ubuntu') {
        sh 'ls && sleep 120'
      }
    }
    stage('Verify artefacts')
      container('gf') {
        sh 'ls /opt'
      }
  }
}
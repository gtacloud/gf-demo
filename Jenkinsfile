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
    command: "--default-authentication-plugin=mysql_native_password"  
"""
  ) {

  node(label) {
    stage('Test env') {
      git 'https://github.com/jenkinsci/docker-inbound-agent.git'
      container('ubuntu') {
        sh 'ls && sleep 120'
      }
    }
  }
}
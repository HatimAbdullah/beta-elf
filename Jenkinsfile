pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    service_name: elf-service
    service_type: REST
spec:
  containers:
  - name: dnd
    image: docker:latest
    command: 
    - cat
    tty: true
    volumeMounts: 
    - mountPath: /.kube/config.yaml
      name: file
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock  
      type: Socket
  - name: kconf
    hostPath:
      path: /etc/rancher/k3s/k3s.yaml
      type: file

"""
    }
  }
  environment {
    DOCKER_NAMESPACE = 'lordblackfish'
    SERVICE_NAME = 'mother-service'
  }
  stages {
      stage("Build") {
          steps {
              container('dnd') {
                  sh """
                      export KUBECONFIG=/kube/config.yaml             
                      helm repo add elastic https://helm.elastic.co
                      helm repo add fluent https://fluent.github.io/helm-charts
                      helm repo update
                      helm install elasticsearch elastic/elasticsearch --version=7.9.0 --namespace=elf -f values.yaml
                  """
              }
          }
      }
  }
}

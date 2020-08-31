pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  name: elf-pod
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
      name: kconf
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
  volumes:
  - name: kconf
    hostPath:
      path: /etc/rancher/k3s/k3s.yaml
      type: File

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
              container('kubectl') {
                  sh """
                      export KUBECONFIG=/kube/config.yaml
                      cat /kube/config.yaml       
                      helm repo add elastic https://helm.elastic.co
                      helm repo add fluent https://fluent.github.io/helm-charts
                      helm repo update
                      helm install fluent-bit fluent/fluent-bit
                  """
              }
          }
      }
  }
}
